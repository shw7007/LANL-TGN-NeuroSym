# Explain TGN simply
TGN(Temporal Graph Network)은 시간에 따라 변화하는 그래프의 이벤트를 처리하는 Dynamic GNN모델로 Dataset의 다음과 같은 특징을 포착하는데 특화되어있습니다.
#### 1. 이벤트 단위의 시간순 데이터(Temporal Event-driven)
TGN은 Input data를 이벤트 형식으로 입력받고 timestampe를 사용하면서 시간 순으로 이벤트가 발생하는 데이터에서 강점을 보입니다.
#### 2. 노드 상태가 시간에 따라 변화하는 데이터(temporal)
시간순으로 데이터를 학습할 때마다 노드의 feature/data가 변화하고 변화된 데이터를 바탕으로 이후 prediction을 수행해야하는 경우 TGN은 이 특징을 memory를 이용해 포착합니다.
#### 3. 그래프 구조가 거대하고 희소(sparse)하며 동적(dynamic)한 경우
TGN은 학습/배포 과정에서 처음보는 노드/객체에 대한 이벤트를 만나더라도 이벤트의 다른 특성을 이용해 prediction을 진행할 수 있습니다.
#### 4. link predictionm, event prediction이 필요한 데이터
TGN은 기본적으로 Event 형식의 dataset에 대해 학습하면 Event형식의 Input에 대한 prediction을 수행하는 model입니다. 

# Modules in TGN

# Shortcomings
LANL 데이터셋은 희소벡터라서 TGN을 이용할 때 spatial 이점을 이용하지 못함
# Paper
Temporal Graph Networks for Deep Learning on Dynamic Graphs : https://arxiv.org/abs/2006.10637
# Docs - pytorch
https://pytorch-geometric.readthedocs.io/en/2.7.0/generated/torch_geometric.nn.models.TGNMemory.html?highlight=tgnmemory#torch_geometric.nn.models.TGNMemory
# Code - pytorch
https://pytorch-geometric.readthedocs.io/en/2.7.0/_modules/torch_geometric/nn/models/tgn.html
