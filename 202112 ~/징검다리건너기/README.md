
## [징검다리건너기](https://programmers.co.kr/learn/courses/30/lessons/64062?language=java)

카카오 초등학교의 "니니즈 친구들"이 "라이언" 선생님과 함께 가을 소풍을 가는 중에 징검다리가 있는 개울을 만나서 건너편으로 건너려고 합니다. "라이언" 선생님은 "니니즈 친구들"이 무사히 징검다리를 건널 수 있도록 다음과 같이 규칙을 만들었습니다.

- 징검다리는 일렬로 놓여 있고 각 징검다리의 디딤돌에는 모두 숫자가 적혀 있으며 디딤돌의 숫자는 한 번 밟을 때마다 1씩 줄어듭니다.
- 디딤돌의 숫자가 0이 되면 더 이상 밟을 수 없으며 이때는 그 다음 디딤돌로 한번에 여러 칸을 건너 뛸 수 있습니다.
- 단, 다음으로 밟을 수 있는 디딤돌이 여러 개인 경우 무조건 가장 가까운 디딤돌로만 건너뛸 수 있습니다.

"니니즈 친구들"은 개울의 왼쪽에 있으며, 개울의 오른쪽 건너편에 도착해야 징검다리를 건넌 것으로 인정합니다.
"니니즈 친구들"은 한 번에 한 명씩 징검다리를 건너야 하며, 한 친구가 징검다리를 모두 건넌 후에 그 다음 친구가 건너기 시작합니다.

디딤돌에 적힌 숫자가 순서대로 담긴 배열 stones와 한 번에 건너뛸 수 있는 디딤돌의 최대 칸수 k가 매개변수로 주어질 때, 최대 몇 명까지 징검다리를 건널 수 있는지 return 하도록 solution 함수를 완성해주세요.

[제한사항]
- 징검다리를 건너야 하는 니니즈 친구들의 수는 무제한 이라고 간주합니다.
- stones 배열의 크기는 1 이상 200,000 이하입니다.
- stones 배열 각 원소들의 값은 1 이상 200,000,000 이하인 자연수입니다.
- k는 1 이상 stones의 길이 이하인 자연수입니다.

-----------
#### 예제

    stones : [2, 4, 5, 3, 2, 1, 4, 2, 5, 1]	
    k : 3
	
	answer 
    3

## 풀이
만약 **5명**의 니니즈 친구들이 징검다리를 모두 건널 수 있는지 알아보기 위해서는 처음부터 마지막까지 건너게 해보는 게 아니라, 가장 마지막인 다섯번째가 건널 수 있다면 5명이 징검다리를 건널 수 있는 것이기 때문에 **다섯번째의 니니즈가 건널 수 있는지만 보면 된다.**

즉 우리는 n번째 니니즈가 건널 수 있는지 판단하고, 건널 수 있다면 **현재 최대 n명의 니니즈가 건널 수 있다고 생각하면 된다.**

이때 이 문제에서 징검다리를 건너야 하는 니니즈 친구들의 수는 무제한이지만 stones 배열의 각 원소의 값은 1부터 200,000,000이하이기 때문에 최대로 건널 수 있는 니니즈 친구의 수는 `1`부터 `200,000,000`이 된다.
(모든 디딤돌의 숫자가 `200,000,000`이라고 생각했을 때)

즉, n(n번째 니니즈)은 1부터 200,000,000이 될 수 있고 `이분 탐색`을 이용해서 n의 범위를 차차 줄여가면서 진행하면 된다.

### 상세 풀이 
n번째 니니즈 친구가 징검다리를 건널 때 디딤돌의 상황은 다음과 같다.

- **n번째** 니니즈 친구가 징검다리를 건너는 상황이라면(n-1번째까지는 모두 건넜다고 가정) 현재 징검다리의 상황은 `디딤돌에 적힌 숫자 - (n-1)`이 된다.

    ```
    처음
    2 4 5 3 2 1 4 2 5 1

    4번째 니니즈가 건너는 상황
    0 1 2 0 0 0 1 0 2 0
    ```

디딤돌의 숫자가 0이 되면 더 이상 밟을 수 없지만 한 번에 k칸까지 건너뛸 수 있다.

- for문을 돌면서 `stones[current_idx] - n < 0` 인지 판단하고 0이하라면 cnt를 늘린다. 건널 수 없는 디딤돌이 k만큼 붙어 있다면(`cnt == k`) 더이상 건너갈 수 없기 때문에 탐색의 범위를 앞으로 줄여서(`right = mid -1`) 다시 진행하면 된다.

만약 n번째 니니즈가 모든 디딤돌을 건넜다면, 더 뒤에 순서에 있는 니니즈(n보다 큰)가 디딤돌을 건널 수도 있기 때문에 탐색의 시작을 n+1(`left = mid+1`)로 하고 다시 진행한다. 그리고 이때 최대로 건널 수 있는 니니즈의 수를 다시 설정한다.

## 코드

```java
public int solution(int[] stones, int k) {
    int answer = 0;
    int min = 1;
    int max = 200000000;

    // 이분탐색을 이용해서 n의 범위를 줄여간다.
    while (min <= max) {
        int currentIdx = (min + max) / 2;
        int zeroCnt = 0;
        boolean isSuccessful = true;

        for (int stone : stones) {
            if (stone - currentIdx < 0) { // currentIdx번째로 징검다리를 건널 때 디딤돌의 상태가 0이하라면
                zeroCnt++; // 건너지 못하는 디딤돌이여서 cnt를 증기시킨다
            } else { // 건널 수 있다면
                zeroCnt = 0;
            }
            if (zeroCnt == k) { // 만약, 연속으로 k만큼 건너지 못하는 디딤돌일 때
                max = currentIdx - 1; // 탐색 범위를 줄인다
                isSuccessful = false;
                break;
            }
        }

        if (isSuccessful) { // currentIdx번째의 니니즈가 건넜다면
            min = currentIdx + 1;
            answer = currentIdx; // 현재 기준으로 최대 currentIdx명의 니니즈가 건널 수 있다.
        }
    }
    return answer;
}	
```
