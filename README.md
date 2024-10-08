# CoT-Reasoning_without_Prompting
구글에서 발표한 Chain-of-Thought Reasoning without Prompting을 코드로 구현한 Repo입니다. 
paulosantosneto/unofficial-cot-decoding님이 일부 구현해놓으셨지만, 한국어는 제대로 작동하지 않았습니다. 
이에, paulosantosneto/unofficial-cot-decoding의 코드를 수정하여 한국어에서도 잘 작동하도록 코드를 보안해보았습니다. 

## 이 논문을 연구하게 된 계기 
'아이의 아버지인 외과의사가 말했어. "난 수술 못해! 이 아이는 내 아들이라고!" 이 외과의사는 소년에게 누구일까요?'
이 간단한 질문의 최근 나온 GPT4-o1는 다음과 같이 답변을 하였습니다. 

<img width="500" alt="image" src="https://github.com/user-attachments/assets/403eda66-0eac-42b9-8893-694f433d8022">

또한, 일반적인 LLM 모델(Gemma-2-2B)에도 물어보았습니다.  
<img width="500" alt="image" src="https://github.com/user-attachments/assets/81b8cdc1-244e-420d-a4fc-b9149709df05">

왜 이러한 간단한 질문에도 LLM은 답변을 하지 못할까 고민을 하고 있는 도중 이 논문을 발견하게 되었습니다. 
논문에서 제시하는 방법을 사용하면 추가적인 학습을 하지 않아도 아래 처럼 답변을 잘하는 것을 볼 수 있습니다. 

<img width="500" alt="image" src="https://github.com/user-attachments/assets/a47828be-dee6-4025-8fb4-f601c0ba8a4d">

이 방법을 사용한다면 특별한 전처리 없이 할루시네이션을 조금이나마 줄일 수 있지 않을까? 생각해보았습니다. 

## 개요 
기존의 문장 단위로 CoT가 이루어졌다면, 구글에서는 토큰 단위로 CoT가 이루어져야 하지 않나? 라는 질문으로 시작된 연구입니다. 즉, greedy Decoding 대신 Top-K개의 토큰 샘플링을 사용하여 여러 추론 경로를 생성하고 평가하며, 각 경로에서 발생하는 추론 과정을 비교합니다. 이를 통해 다양한 사고 과정을 평가할 수 있으며, 최종적으로 더 신뢰할 수 있는 답변을 도출할 수 있다고 저자들을 주장합니다. 

## 논문과 매칭 
이 코드는 논문에서 제안된 Chain-of-Thought 추론 경로 탐색 방식을 실제로 구현한 것입니다. 논문에서는 CoT 추론이 복잡한 문제를 해결하는 데 매우 효과적이라고 설명하며, 특히 top-k 토큰 샘플링을 이용해 다양한 사고 과정을 탐색하고 평가하는 방법론을 강조합니다.

* Greedy Decoding 대신 Top-k Sampling 사용: 논문에서는 greedy decoding 방식이 하나의 경로만을 탐색하는 한계가 있다고 설명하며, 이를 극복하기 위해 top-k 토큰 샘플링을 사용하여 다양한 추론 경로를 탐색할 것을 권장하였는데, 본 코드에서 이를 구현하기 위해 get_first_topk_tokens 및 generate_paths 함수를 사용하여 top-k 개의 토큰을 샘플링하고, 이를 기반으로 여러 경로를 생성합니다.

* 다양한 사고 과정 평가: 논문에서는 여러 가지 사고 경로를 생성하여 이를 비교하고 평가하는 방식이 중요하다고 강조합니다. 이를 위해 코드는 calculate_score 함수에서 각각의 답변 경로에 점수를 부여합니다.

## 논문에는 없지만 추가해본 내용 
* 패널티 적용: 논문에서는 언급되어 있지 않지만, 한국어의 경우 적절한 답을 찾아내지 못하는 경우가 발생되었습니다. 예를 들어, 질문 반복이나 불완전한 답변에 높은 점수를 주는 것을 관찰했습니다. 이에, 질문과 유사한 답변을 식별하고 패널티를 부여하여 정답을 정확하게 평가할 수 있도록 추가 설계하였습니다. 

