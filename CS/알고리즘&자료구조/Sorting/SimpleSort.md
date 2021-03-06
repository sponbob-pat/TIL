## 버블 정렬 : Bubble Sort

정렬 방법 중 가장 잘 알려준 정렬방법이다. 이해와 구현은 쉽지만 성능에는 아쉬움이 있다.  
그럼 3, 2, 4, 1 순서대로 저장된 다음 그림의 배열을 `오름차순(큰 값이 뒤에 있는 방법)`으로 정렬하는 과정을 보자.

### 과정 

- 1단계 

![image](https://user-images.githubusercontent.com/64796257/150472504-d0f86536-3ddf-442b-a689-bbc918c50523.png)

`버블 정렬`은 인접한 두 개의 데이터를 비교해가면서 정렬하는 방식이다.   
두 데이터를 비교하면서 정렬순서상 위치가 바뀌어야 하는 경우에는 두 데이터의 위치를 바꿔나간다. 

여기서는 `오름차순`으로 정렬해야 하기 때문에 `큰 값이 오른쪽으로` 이동해야 한다.  
즉, 두 데이터를 비교했을 때 왼쪽 데이터가 오른쪽 데이터보다 크다면 서로 위치를 바꾼다.

⇒ 이렇게 1단계를 마치고 나면, 가장 큰 값이 가장 오른쪽에 위치하게 된다. 이제 2단계에서는 2번째로 큰 값을 4의 왼쪽에 위치시켜야 한다.

- 2단계 

![image](https://user-images.githubusercontent.com/64796257/150472613-c6dacdcc-69da-4301-8c28-c0eea92fcd20.png)

똑같이 한다. 인접한 두 데이터를 비교해서 왼쪽의 값이 크면 오른쪽으로 이동시킨다.

- 3단계 

![image](https://user-images.githubusercontent.com/64796257/150472638-cfb8c083-5f83-46d7-b49e-5152e4e27287.png)

### 구현 

이제 남은 두 개의 데이터는 2와 1을 정렬하면 최종적으로 정렬이 완료된다.
``` java
// 배열에 어떤 자료형이 올지 모르기 때문에 Generic을 이용해서 T라고 정했다.
// 이와 같은 정렬을 하고 나면 배열 array는 오름차순으로 정렬된다. 

// swap, greater는 sortutil 클래스에 정의되어 있다. 

public <T extends Comparable<T>> T[] sort(T[] array) {
    for (int i = 0, size = array.length; i < size - 1; ++i) {
      for (int j = 0; j < size - 1 - i; ++j) {
    	  
    	// array[j]가 array[j+1]보다 클 때
    	// 즉, 왼쪽 원소의 값이 오른쪽 원소 값보다 클 때
        if (greater(array[j], array[j + 1])) {
          swap(array, j, j + 1);
          // 서로 값을 바꾼다. 
          // array 행렬의 j번째 값과 j+1번째 값을 바꾼다.
        }
      }

    }
    return array;
  }
```

### 성능 평가 
정렬 알고리즘의 성능은 다음 두 가지를 근거로 판단하는 것이 일반적이다. 두 연산은 정렬과정의 핵심연산이기 때문.
- `비교연산` : 두 데이터간의 비교연산의 횟수
- `대입연산` : 위치의 변경을 위한 데이터의 이동횟수

실제로 시간 복잡도에 대한 Big-O를 결정하는 기준은 `비교의 횟수`이다.  
여기에 `이동횟수`까지 살펴본다면 Big-O의 복잡도를 갖는 알고리즘간의 세밀한 비교가 가능하다.

버블 정렬의 비교횟수는 다음 반복문 안에 위치한 if문의 실행 횟수를 기준으로 계산할 수 있다.  
![image](https://user-images.githubusercontent.com/64796257/150474559-d47b0579-7d40-4cb3-ae9c-82d0e9ccba7f.png)

하지만 이 경우는 그림으로 설명한 알고리즘의 동작 방식을 근거로 비교의 횟수를 계산하는 것이 훨씬 간단하다.  
배열에 담긴 4개의 데이터를 3단계에 걸쳐서 정렬했는데... 단계별로 진행된 비교횟수를 더한 결과는 3+2+1이다.

따라서, 데이터의 개수가 n일 때 진행되는 비교횟수는 

![image](https://user-images.githubusercontent.com/64796257/150474614-b615a355-1ce9-4d59-b9c6-a68d971a4a7b.png)

이동횟수의 최악의 경우를 보면 한 번 단계를 거칠 때 마다 3번씩 이동한다는 걸 알 수 있다. 그래서 시간복잡도는 똑같이 O(n^2) 이 된다.

## 선택 정렬 : Selection sort 

### 과정 
![image](https://user-images.githubusercontent.com/64796257/150474727-3effb8a3-3fc6-466c-83b6-2dca2725971b.png)

선택정렬은 그림에서 보이는 바와 같이 정렬 대상에 있는 데이터를 정렬순서에 맞게 하나씩 선택하고 옮기면서 정렬하는 알고리즘이다.

여기서 별도의 메모리 공간이 필요하다는 걸 알 수 있다. 

하지만 데이터를 하나씩 옮길 때 마다 공간이 하나씩 비기 때문에 아래와 같이 알고리즘을 개선하면 별도의 메모리 공간을 마련할 필요는 없다.

![image](https://user-images.githubusercontent.com/64796257/150474870-c1b3e0f1-a4e1-49f2-9707-07dc563c8015.png)

정렬순서상 가장 앞서야 하는 것을 선택해서 가장 왼쪽으로 이동시키고 원래 그 자리에 있던 데이터는 빈 자리에 놓는다.

이를 구현하려면 임시적으로 데이터를 저장할 변수 하나는 선언해놓아야 한다.

### 구현
``` java
  public <T extends Comparable<T>> T[] sort(T[] arr) {
    int n = arr.length; // 배열의 크기 n
    
    for (int i = 0; i < n - 1; i++) {
      int minIndex = i; // 일단 i값을 minIndex에 저장한다.
      for (int j = i + 1; j < n; j++) {
      
      // minIndex번째 값이 j번째 값보다 크다면 minIndex에 j를 저장한다.
        if (arr[minIndex].compareTo(arr[j]) > 0) {
          minIndex = j;
        }
      }
      // 기존에 저장한 minIndex 값이 달라졌다면 
      // i번째 값과 minIndex번째 값을 서로 바꾼다.
      if (minIndex != i) {
        // swap(arr, i, minIndex); 과 동일한 내용
        T temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
      }
    }
    return arr;
  }
```

### 성능 평가 
바깥쪽 for문의 i = 0일 때 안쪽 for문의 j =  1 ~ (n-1) 까지 증가하면서 비교연산을 n-1번 진행한다.
i = 1일 때 안쪽 for문의 j = 2 ~ (n-1)까지 증가하면서 연산을 n-2번 진행한다.

이런식으로 진행되는 비교연산의 횟수는 다음과 같다. 

![image](https://user-images.githubusercontent.com/64796257/150484054-c2b0808f-61da-4573-a235-c9f1fd3cbdd9.png)

이동횟수와 교환횟수는 O(n^2)이라는 시간복잡도에 영향을 끼치지 않기 때문에 계산에 넣지 않았다.

## 삽입 정렬(Insertion Sort) 

### 과정 

아래 그림은 정렬이 `완료된 부분`과 `완료되지 않은 부분`으로 나뉘어 있다.  
이렇듯 `삽입 정렬`은 정렬 대상을 두 부분으로 나눠서 `정렬이 안 된 부분에 있는 데이터`를 `정렬된 부분의 특정 위치`에 `삽입`하면서 정렬을 진행하는 알고리즘이다.

![image](https://user-images.githubusercontent.com/64796257/150484923-ecef483e-6d40-48d8-bb3b-dc46b64d25c0.png)

5, 3, 2, 4, 1의 오름차순 정렬과정을 살펴보도록 하자.

![image](https://user-images.githubusercontent.com/64796257/150484953-faae2588-6853-4934-955a-17dede5fbbe1.png)

`첫 번째 데이터`와 `두 번째 데이터`를 비교해서 정렬된 상태가 되도록 두 번째 데이터를 옮기면서 정렬이 시작된다.

이 과정을 통해 첫 번째/두 번째 데이터가 `정렬이 완료된 영역`을 형성하게 된다.

곧이어 `세 번째/네 번째 데이터`가 정렬이 완료된 영역으로 삽입되면서 정렬을 이어간다. 

여기서 정렬된 영역으로 삽입하기 위해서는 특정위치를 비워야 하고 비우기 위해서는 데이터를 한 칸씩 뒤로 미는 연산을 수행해야 한다. 

다음 그림을 보자.

![image](https://user-images.githubusercontent.com/64796257/150485107-f1ea53ab-781f-4192-a591-1382b9a02f65.png)

삽입위치를 찾는 과정과 삽입을 위한 공간 마련의 과정을 구분할 필요가 없다.

어차피 정렬된 영역에서 삽입의 위치를 찾는 것이기 때문에 삽입위치를 찾으면서 삽입을 위한 공간의 마련을 병행할 수 있다.

### 구현 

``` java
  public <T extends Comparable<T>> T[] sort(T[] array) {
    for (int i = 1; i < array.length; i++) {
      T insertValue = array[i]; // 삽입할 데이터를 insertValue 변수에 저장 
      int j;
      
      // j >= 0 이고 insertValue가 array[j]보다 작으면 for문을 실행한다.
      for (j = i - 1; j >= 0 && less(insertValue, array[j]); j--) {
        array[j + 1] = array[j];
      }
      
      if (j != i - 1) {
        array[j + 1] = insertValue;
      }
    }
    return array;
  }
```

### 성능 평가 

최악의 경우를 고려해보면 안쪽 for문의 if-else문의 조건이 항상 참이 되면서 break가 단 한 번도 발생하지 않을 때가 최악의 경우이다.  
그래서 바깥쪽 for문의 반복횟수와 안쪽 for문의 반복횟수를 곱한 만큼 연산이 진행된다.

따라서, Big-O는 O(n^2) 가 된다는 걸 알 수 있다.










