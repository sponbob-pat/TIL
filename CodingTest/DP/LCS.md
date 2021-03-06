## LCS(Longest Common Sub-Sequence) 

### 조건 및 규칙

두 문자열 `X_m`과 `Y_n`이 있다. 여기서 `m,n`은 `X_m과 Y_n 문자열`의 각각의 `길이`를 의미한다.

이때, 두 문자열의 `공통 부분`이 있을 수 있는데 공통 부분 중에서 `가장 긴 공통 부분의 길이`가 뭔지 알아내려 한다.  
⇒ 이를 이용한 것이 git의 `diff`가 되겠다.

뭔 말인지 이해하기 힘들 수 있으니 단계적으로 의미를 살펴보자.
- sub-sequence
ex. A**BC**B**D**A**B**가 있다고 할 때 해당 문자열 중에서 **BCDB**를 sub-sequence라 할 수 있다.

![image](https://user-images.githubusercontent.com/64796257/150727452-04096e11-1fc8-4d58-a178-faf3b501167e.png)

### DP 조건 만족 여부 및 구현

그럼 이것도 동적 프로그래밍을 적용할 수 있을까? 먼저 `최적 부분 구조`를 살펴보자.

![image](https://user-images.githubusercontent.com/64796257/150727486-47a98848-885b-42b8-8341-6b303cd075e2.png)

1) ![image](https://user-images.githubusercontent.com/64796257/150727494-5b99ca05-e829-4088-82d3-efb534486055.png)
이라면, 

`X_m`과 `Y_n`의 `LCS`는 `X_(m-1)과 Y_(n-1)의 LCS`보다 `1 크다`.  
왜냐하면, 이미 각 문자열의 끝 부분인 `x_m과 y_n`이 `서로 같아서` 해당 문자열의 이전 부분인 `X_(m-1)과 Y_(n-1)의 LCS`를 구하고  
`x_m = y_n`은 성립하므로 `1`만 더해주면 된다.

즉, `(m,n) 크기 문제의 해`는 `(m-1, n-1) 크기 문제의 해`를 `포함`한다고 할 수 있다.

2) ![image](https://user-images.githubusercontent.com/64796257/150727670-94e5c4ed-06d4-4a91-a933-527aacd75c32.png)
이면, 

`X_m과 Y_n의 LCS 길이`는 이미 ![image](https://user-images.githubusercontent.com/64796257/150727721-6435611f-b1a5-4856-8d97-58d60d62ffc0.png)
라는 조건을 내걸었기 때문에 X_m과 Y_n을 굳이 `다 비교할 필요는 없다`. 

그래서 `x_m` 또는 `y_n`을 `하나씩 제외`하고 `각각의 문자열을 비교`한다.  
그러면, `X_m과 Y_(n-1) 사이의 LCS`와 `X_(m-1)과 Y_n 사이의 LCS`를 각각 구해서 `더 큰 값`이 `X_m과 Y_n의 LCS`가 되겠다.

![image](https://user-images.githubusercontent.com/64796257/150727818-a0746e29-7712-47fe-b3c9-ab31090bbf34.png)

이를 통해 점화식을 구하면 다음과 같다.

![image](https://user-images.githubusercontent.com/64796257/150727883-21ec6a68-b1a0-4c5d-9564-838f80880f71.png)

`C_(i,j)`는 ![image](https://user-images.githubusercontent.com/64796257/150727900-032d60b1-9983-4dd2-9246-62a6c57f244b.png) 과 ![image](https://user-images.githubusercontent.com/64796257/150727911-3a751ad1-538d-464e-b2ae-54ca76084901.png)
의 LCS라 하자.

![image](https://user-images.githubusercontent.com/64796257/150728002-c03ae0bc-a6f8-4612-b581-27decec21ab9.png)

여기서 끝내면 중복 호출을 많이 발생시킨다. 그래서 아래서부터 시작해서 각각의 계산 결과값을 저장하면서 재귀적으로 함수를 진행한다.

![image](https://user-images.githubusercontent.com/64796257/150728046-e66e9610-b721-4c1b-9bd6-efaa9cd51047.png)

이렇게 되면 최종적으로 `길이가 m인 문자열`과 `길이가 n인 문자열`의 `LCS`를 구할 수 있다. 

`시간복잡도`는 m번 반복하는 for문을 n번 반복하므로 `mn`이 되고

`공간복잡도`는 `i가 1부터 m까지` 이고 `j가 1부터 n까지` 이므로 필요한 `C_(i,j)`의 저장 공간개수는 `mn`이 된다.

따라서, 둘 다 `Θ(mn)`이다.

### 예제 입력 1 

```
ACAYKP
CAPCAK
```

### 예제 출력 1

```
4

ACAK 가 공통 부분이다.
```


## 구현 코드 

``` java
import java.io.IOException;

import java.io.InputStreamReader;

import java.io.BufferedReader;

public class LCS {

	static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
  // 배열을 1부터 시작하기 위해서 제한조건인 문자열 길이 1000에 1을 더해서 배열을 만들어놨다.
	static int[][] common = new int[1001][1001];
	
	static int lcs(String str1, String str2, int m, int n) {
		
    // 초기 설정
		for(int i = 0; i <= m; i++) common[i][0] = 0;
		
		for(int j = 0; j <= n; j++) common[0][j] = 0;
		
    // 
		for(int i = 1; i <= m; i++) {
			
			for(int j = 1; j <= n; j++) {
				
        // 맨 끝에 있는 두 문자가 서로 같을 때 아래의 동작을 한다.
				if(str1.charAt(i-1) == str2.charAt(j-1)) {
					
					common[i][j] = common[i-1][j-1] + 1;
					
				}
				// 서로 같지 않을 때 아래의 동작을 한다.
				else {
					common[i][j] = Math.max(common[i-1][j], common[i][j-1]);
				}
			}
		}
		return common[m][n];
	}
	
	public static void main(String[] args) throws IOException {
	
		String s1 = br.readLine();
		String s2 = br.readLine();
		
		// s1.charAt(2);- s1 문자열의 2번째 값(물론 시작은 0부터)
		
		System.out.println(lcs(s1, s2, s1.length(), s2.length()));
		

	}
}

```
