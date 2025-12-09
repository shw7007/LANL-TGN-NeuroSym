# Explain TGN simply
TGN(Temporal Graph Network)은 그래프 위에서 시간순으로 발생하는 이벤트를 처리하는 Dynamic GNN 모델이며,다음과 같은 특징을 포착하는데 특화되어있습니다.
#### 1. 이벤트 단위의 시간순 데이터(Temporal Event-driven)
TGN은 Input data를 이벤트 형식으로 입력받고 timestampe를 사용하면서 시간 순으로 이벤트가 발생하는 데이터에서 강점을 보입니다.
#### 2. 노드 상태가 시간에 따라 변화하는 데이터(temporal)
시간순으로 데이터를 학습할 때마다 노드의 feature/data가 변화하고 변화된 데이터를 바탕으로 이후 prediction을 수행해야하는 경우 TGN은 이 특징을 memory를 이용해 포착합니다.
#### 3. 그래프 구조가 거대하고 희소(sparse)하며 동적(dynamic)한 경우
TGN은 학습/배포 과정에서 처음보는 노드/객체에 대한 이벤트를 만나더라도 이벤트의 다른 특성을 이용해 prediction을 진행할 수 있습니다.
#### 4. link predictionm, event prediction이 필요한 데이터
TGN은 기본적으로 Event 형식의 dataset에 대해 학습하면 Event형식의 Input에 대한 prediction을 수행하는 model입니다. 

# How TGN works
TGN은 이벤트 데이터를 바탕으로 그래프 위에 노드와 엣지를 구성합니다. 
이때 TGN의 각 노드는 초기엔 0벡터로 초기화된 memory라는 상태변수를 갖게됩니다. TGN은 이벤트가 주어지면, 이와 관련된 노드들의 memory를 참조하여 embedding을 만들고, embedding을 통해 예측을 실시합니다. 이 예측과 주어진 이벤트를 비교하여 학습을 진행하며, 배포모델에서는 동일한 방식으로 주어진 이벤트에 대해 예측을 진행합니다. 

# Modules in TGN
TGN의 모듈은 다음과 같이 구성돼있다.

1. Memory Module
2. Message Function
3. Aggregator
4. Temporal Encoding
5. Node/Edge Embeddiing
6. Prediction Head

먼저, TGN에 입력되는 모든 Dataset(학습/배포 과정 모두에서)은 다음과 같은 형태를 띕니다.

`(src, dst, timestamp, edge_features)`
###  1.Memory Module
TGN은 Graph구조로 Dataset을 해석합니다. Graph의 node는 Dataset에서 등장하는 모든 `src, dst`가 되며 각 node는 **memory**라는 상태 변수(state variable)을 가집니다.

`class TGNMemory(num_nodes: int, raw_msg_dim: int, memory_dim: int, time_dim: int, message_module: Callable, aggregator_module: Callable)`

`TGNMemory` class는 생성시 자체적으로 memory변수를 만들어 유지합니다. 이는 TGN에서 가장 중요한 module입니다.

### 2. Message Function
Dataset에 포함된 Event가 학습가능한 형태, 혹은 예측 가능한 형태가 되기 위해서는 Message Function을 이용해 Message 형태로 만들어야합니다.

`IdentityMessage(raw_msg_dim, memory_dim, time_dim)` 

`IdentitiyMessage`는 `TGNMemory`의 `message_module`파트에 사용되며 Event(raw message)를 넣으면 내장된/설정한 방법에 따라 학습/예측 가능한 Message 형태로 만들어줍니다. 이외에도 다른 형태의 Message Function module이 존재합니다.

### 3. Aggregator
TGN은 병렬처리의 이점을 이용하기 위해 이벤트들을 학습할 때 이벤트를 학습하고 메모리를 반복하는 일을 배치 단위로 수행합니다. 다시 말해, 하나의 이벤트를 학습하고 이러한 이벤트 정보를 메모리에 바로 반영하는 것이 아니라 배치 단위의 이벤트들을 학습한 뒤에 배치 단위의 이벤트들의 정보를 한꺼번에 메모리에 반영합니다.

이때, 배치 내에 하나의 노드에 대한 이벤트가 여러 개 있다면, 메모리는 여러 개의 이벤트를 적절히 고려해 업데이트 되어야합니다.

`LastAggregator()`는 배치 내에서 각 노드의 메모리에 반영되는 이벤트는 최신 이벤트만 사용하는 방식입니다.
이외에도 MeanAggregator등 다양한 module이 있습니다.
### 4. Temporal Encoding
Pass

### 5. Node/Edge Embeddiing
주어진 이벤트에 대해 학습을 하기 위해선, 노드들의 임베딩이 필요합니다. TGN은 각 노드들의 상태변수인 메모리와 GNN기법을 이용해 임베딩을 생성합니다.

이때, 어떤 GNN을 사용할지, 즉 이웃들의 정보를 어떤 식으로 취합해서 node의 임베딩을 생성할지 모델의 목적에 따라 결정할 수 있습니다.

`class TransformerConv(......)`

TransformerConv는 이웃 노드들의 중요도를 계산해 중요도에 비례해서 이웃들의 정보를 집계하는 방식입니다.
TRanformerConv외에도 다른 다양한 GNN module을 이용할 수 있습니다.

### 6. Prediction Head
예측을 최종적으로 만들기 위해 노드들의 임베딩을 바탕으로 예측을 만들어 내는 prediction head가 필요합니다.

`nn.Sequential(nn.Linear(embedding_dim * 2, embedding_dim),nn.ReLU(),nn.Linear(embedding_dim, 1))`

이는 가장 널리 쓰이는 MLP prediction head로 이외에도 다양한 형태의 prediction head를 사용할 수 있습니다.


# Shortcomings
LANL 데이터셋은 희소벡터라서 TGN을 이용할 때 spatial 이점을 이용하지 못함
# Paper
Temporal Graph Networks for Deep Learning on Dynamic Graphs : https://arxiv.org/abs/2006.10637
# Docs - pytorch
https://pytorch-geometric.readthedocs.io/en/2.7.0/generated/torch_geometric.nn.models.TGNMemory.html?highlight=tgnmemory#torch_geometric.nn.models.TGNMemory
# Code - pytorch
https://pytorch-geometric.readthedocs.io/en/2.7.0/_modules/torch_geometric/nn/models/tgn.html
