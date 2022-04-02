# Vuex

vux란 VueJS의 상태관리 라이브러리로 애플리케이션의 모든 컴포넌트에 대한 중앙 집중식 저장소 역할을 하며 의도적인 방법으로 상태를 변경 및 관리할 수 있습니다.

기존 Flux 아키텍쳐를 따라가고 있고, React로 본다면 Redux와 비교할 수 있습니다.

컴포넌트는 일반적으로 `부모-자식`의 관계를 가지고 props와 event를 통해 데이터가 흐른다. vuex는 중앙 집중식 저장소이기 때문에 props와 event의 얽매이지 않아도 됩니다. 때문에 컴포넌트의 구조가 복잡한 경우에는 props와 event를 통한 데이터 전달보다 vuex를 통해 저장소에서 데이터를 관리하는것이 좋습니다.



## Vuex의 구조

vuex는 state, mutations, action, getter 4가지의 형태로 관리가 됩니다. 이때 관리 포인트는 store 패턴을 사용하고 통산 store라고 불립니다.



### State

state는 vue 컴포넌트에서 data로 볼 수 있습니다. 원본 소스의 역할을 하며, view와 직접적으로 연결되어있는 model입니다. state는 직접적인 변경은 불가능하고 mutation을 통해서만 가능합니다. mutation을 통해 state의 업데이트가 일어나면 반응적으로 view과 업데이트 됩니다.



### Mutations

Mutation은 state를 변경하는 유일한 방법이고 이밴트와 유사합니다. 함수로 구현되며 2가지의 인자를 받을 수 있습니다. 첫번째 인자는 state이며 두번째 인자는 payload입니다. mutation은 일반적으로는 직접 호출할 수 없으며, `commit`을 통해서만 호출할 수 있습니다.(...mapMutation 제외)

> 대부분 실무에서는 mutations에서는 API를 통해 전달받은 데이터의 가공하여 state를 설정하는 데 많이 사용됩니다.

```js
store.commit('setData', payload)
```



### Actions

Action은 mutation과 비슷하지만 비동기 동작이 가능하다는 점에서 다릅니다. 또한 mutation에 대한 commit이 가능하며 action에서 mutation을 통해 state을 변경할 수 있습니다. action에서는 첫번째 인자를 context인자를 받을 수 있고 context에는 state, commit, dispatch, rootState와 같은 속성들을 포함합니다. 두 번째 인자는 payload를 받을 수 있습니다.

Action은 `dispatch`를 통해서 호출합니다. context의 속성을 보면 dispatch가 있는 것으로 보아 action에서는 서로 다른 action을 호출할 수 있다는 것을 볼 수 있다.

```js
store.dispatch('setData', payload)
```



### Getters

Getters는 쉽게 Computed로 볼 수 있습니다. getter의 결과는 종속성에 따라 캐시되고 일부 종속성이 변경된 경우에만 다시 재계산됩니다. 이것은 특정 state에 대해 어떠한 연산을 하고 그 결과를 View에 바인딩 할 수 있스며, state의 변경 여부에 따라 getter는 재계산 되고 View역시 업데이트를 일으킨다. **이때 state는 원본 데이터로서 변경이 일어나지 않습니다.**



### Vuex Binding Helper

모듈을 생성하고 Vuex.Store에 바인딩할 때 우리가 직접 정한 명칭을 사용하여 접근하여야 합니다. 만약 store의 구조가 깊고 복잡한 구조라면 state 하나를 바인딩하기 위해서 긴 값을 코딩해야 합니다. 이런 이유로 Vuex에는 Helper라는 Util이 존재합니다..

Helper는 mapState, mapActions, mapMutations, mapGetters가 존재하고 아래처럼 바인딩할 수 있습니다.

```js
import { mapState, mapActions, mapMutations, mapGetter } from 'vuex';

export default {
  name: 'Sample',
  computed: {
    ...mapState({
        message: state => state.message     // -> this.message
    }),
    ...mapGetters([
       'getMsg'       // -> this.getMsg
    ])
  },
  methods: {
    ...mapMutations([
        'changeMessage'     // -> this.changeMessage()
    ]),
    ...mapActions([
        'callMutation'      // -> this.callMutation()
    ])
  }
}
```

Helper를 사용하게 되면 코드의 가독성이 훨씬 좋아집니다.





## Flux 패턴

flux란 mvc 패턴의 복잡한 데이터 흐름 문제를 해결하는 패턴입니다.

`Action` -> `Dispatcher` -> `Model` -> `View`

Action : 화면에서 발생하는 이밴트 또는 사용자의 입력

Dispatcher : 데이터를 변경하는 방법, 메서드

Model : 화면에 표시할 데이터

View : 사용자에게 비춰지는 화면