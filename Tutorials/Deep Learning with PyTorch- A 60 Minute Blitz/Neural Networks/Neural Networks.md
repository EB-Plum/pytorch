## Neural Networks

신경망(Neural Networks)은 `torch.nn` 패키지에 구성되어 있습니다.
`autograd`와 `nn`을 간략하게 설명하면, 모델들을 정의하고 구별하는 역할을 합니다. `nn.Module`은 신경망의 레이어와 결과를 반환하는 메소드로 `forward(input)`을 입력받아 `output`을 출력합니다.

간단한 예제를 살펴보면, 숫자 이미지를 분류하는 네트워크는 다음과 같습니다.

![](https://github.com/V-AIS/pytorch/blob/master/Tutorials/Deep%20Learning%20with%20PyTorch-%20A%2060%20Minute%20Blitz/Neural%20Networks/mnist.png)

단순한 피드 포워드 네트워크(Feed-Forward Network)입니다. 입력을 받아, 여러 레이어를 차례로 전달한 다음 최종적으로 출력을 제공합니다.

신경망의 일반적인 학습 단계는 다음과 같습니다.

- 학습 가능한 매개변수(또는 가중치)가 있는 신경망을 정의합니다.
- 입력 데이터셋에 대해 반복
- 네트워크를 통해 입력 프로세스 (설계된 네트워크 구조와 초기화된 가중치에 대해 전방 계산을 의마하는 듯 합니다. [Process input through the network])
- 출력이 정확해질 때 까지 손실(Loss)을 계산합니다.
- 그라디언트를 네트워크 매개변수로 다시 전파합니다. (Back-Propagate 방법)
- 가중치를 업데이트 합니다. 일반적으로 간단한 법칙은 다음과 같습니다. `weight = weight - learning_rate * gradient`

---
원문 내용을 남깁니다. (번역이 조금 이상함)
- Define the neural network that has some learnable parameters (or weights)
- Iterate over a dataset of inputs
- Process input through the network
- Compute the loss (how far is the output from being correct)
- Propagate gradients back into the network’s parameters
- Update the weights of the network, typically using a simple update rule: weight = weight - learning_rate * gradient


### Define the Network

다음과 같이 정의된 네트워크가 있습니다.

```python
import torch
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):

    def __init__(self):
        super(Net, self).__init__()
        # 1 input image channel, 6 output channels, 5x5 square convolution
        # kernel
        self.conv1 = nn.Conv2d(1, 6, 5)
        self.conv2 = nn.Conv2d(6, 16, 5)
        # an affine operation: y = Wx + b
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        # Max pooling over a (2, 2) window
        x = F.max_pool2d(F.relu(self.conv1(x)), (2, 2))
        # If the size is a square you can only specify a single number
        x = F.max_pool2d(F.relu(self.conv2(x)), 2)
        x = x.view(-1, self.num_flat_features(x))
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

    def num_flat_features(self, x):
        size = x.size()[1:]  # all dimensions except the batch dimension
        num_features = 1
        for s in size:
            num_features *= s
        return num_features


net = Net()
print(net)
```

Out:
```python
Net(
  (conv1): Conv2d(1, 6, kernel_size=(5, 5), stride=(1, 1))
  (conv2): Conv2d(6, 16, kernel_size=(5, 5), stride=(1, 1))
  (fc1): Linear(in_features=400, out_features=120, bias=True)
  (fc2): Linear(in_features=120, out_features=84, bias=True)
  (fc3): Linear(in_features=84, out_features=10, bias=True)
)
```

