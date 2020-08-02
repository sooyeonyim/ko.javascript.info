이 문제는 비교 연산자와 후위/전위형 연산자를 함께 사용하는 경우 어떤 차이가 있는지 보여줍니다.

1. 전위형 증가 연산자를 사용한 경우엔 **1부터 4까지** 출력됩니다.

    ```js run
    let i = 0;
    while (++i < 5) alert( i );
    ```

    `++i`는 `i`를 먼저 증가시키고 새로운 값을 반환하기 때문에 첫 번째 while 반복문에선 1과 5를 비교(`1 < 5`)하고, 얼럿 창엔 `1`이 출력됩니다.

    `1`에 이어서 `2, 3, 4…`이 출력됩니다. `i` 앞에 `++`가 붙어있기 때문에 `5`는 항상 증가 이후의 값과 비교됩니다.

    `i = 4` 이후에 `i`의 값이 `5`로 증가하면 `while(5 < 5)`안의 비교가 실패하기 때문에 반복문은 멈춥니다. 따라서 `5`는 출력되지 않습니다.
2. 후위형 증가 연산자를 사용한 경우엔 **1부터 5까지** 출력됩니다.

    ```js run
    let i = 0;
    while (i++ < 5) alert( i );
    ```

    후위 증가 연산자를 적용하면 `i++`는 `i`를 증가시키긴 하지만 *기존* 값을 반환합니다. 따라서 첫 번째 while 반복문에선 0과 5를 비교(`0 < 5`)합니다. 이 점이 전위 증가 연산자와의 차이입니다.

    그런데 `alert`문은 조건문과 별개의 문이므로 얼럿창엔 `1`이 출력됩니다. `i`는 이미 증가한 이후이기 때문이죠.

    `1`이 출력된 이후에 `2, 3, 4…`가 이어서 출력됩니다.

    `i = 4`일 때 잠시 생각을 가다듬어 봅시다. 전위 증가 연산자(`++i`)를 사용하면 값이 먼저 증가하기 때문에 5와 `5`를 비교하게 되는데, 여기선 후위 증가 연산자(`i++`)를 사용하고 있으므로 `i`는 증가하지만 기존 값인 `4`가 비교에 사용됩니다. 따라서 `while(4 < 5)`가 되고, 해당 조건은 참이므로 하단 블록이 실행되어 `alert` 창이 뜨게 됩니다.

    다음 반복문은 `while(5 < 5)`이므로 마지막 출력되는 값은 `5`가 됩니다.