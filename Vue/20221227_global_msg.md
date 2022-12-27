date: 2022.12.27.Tue.

# :memo: 1. Global Message (Alert / Confirm) 가져다 쓰기(with Vuex)

컴포넌트 어디에서든지 전역에서 쓸 수 있는 `Global Message` 적용법을 다시 터득해서 정리하고자 올린다.<br/><br/>

> 1. 전역에서 쓸 컴포넌트를 따로 만든다. (공통된 디자인을 제외한 메세지 부분은 `<slot></slot>` 형태로 만든다.) <br/>
> 2. 컴포넌트는 열고 닫을 수 있는 `v-model`을 `Vuex`에서 전역변수로 설정한다. <br/>
> 3. 전역 변수를 참조하는 전역 함수인 `Mutation`에서 커스텀 설정을 한다. (잠깐 띄웠다가 3초 후에 사라진다든지...?) :cry:

<br/><br/>

## 커스텀한 Global Message
### 1) Vuex (Store Module 사용)
```js
// vuex store
export default {
  namespaced: true, // 모듈 이용시 필수

  // 전역 변수
  state: {
    // 1. Alert 스타일
    globalAlert: {
      show: false, // 컴포넌트를 열고 닫을 때 참조할 변수
      msg: '', // 컴포넌트에 들어갈 메세지 변수
    },
    // 2. Confirm 스타일
    globalConfirm: {
      show: false,
      msg: '',
      apply: () => {}, // Alert과 다른 점은 [OK] 이벤트가 있으므로 콜백함수를 별도로 추가해 준다
    },
  },

  // 변수를 가져가기 위한 게터 (조작 시 세터는 별도로 만들어야)
  getters: {
    globalAlert(state) {
      return state.globalAlert;
    },

    globalConfirm(state) {
      return state.globalConfirm;
    },
  },

  // 전역 함수
  mutations: {
    globalAlert(state, value) {
      state.globalAlert = value;
      // 3초 뒤에 닫히게 하기 위해 setTimeout 설정
      setTimeout(() => {
        state.globalAlert = {
          ...state.globalAlert,
          show: false,
          msg: null,
        }
      }, 3000);
    },

    globalConfirm(state, value) {
      state.globalConfirm = value;
    },
  },

}
```

<br/>

### 2) 메세지 컴포넌트 (Vuetify2 사용), CSS 배제
```html
<!-- 1. Alert 스타일 -->
<template>
  <v-dialog
    :value="globalAlert"
    @click:outside="$emit('click:cxl')"
  >
    <v-card>
      <slot></slot>
    </v-card>
  </v-dialog>
</template>

<script>
  export default {
    props: {
      globalAlert: {
        type: Boolean,
        required: true,
      },
    },
  };
</script>


<!-- 2. Confirm 스타일 -->
<template>
  <v-dialog
    :value="globalConfirm"
    @click:outside="$emit('click:cxl')"
  >
    <v-card>
      <slot></slot>
    </v-card>
    <!-- Confirm을 위한 버튼 별도로 만듦 -->
    <v-card-actions>
      <v-btn @click="$emit('click:cxl')">
        취소
      </v-btn>
      <v-btn @click="$emit('click:apply')">
        확인
      </v-btn>
    </v-card-actions>
  </v-dialog>
</template>

<script>
  export default {
    props: {
      globalConfirm: {
        type: Boolean,
        required: true,
      },
    },
  };
</script>
```

<br/>

### 3) 최상위 컴포넌트에 Global Message 컴포넌트 배치
<u>왜 최상위 컴포넌트에 배치하는가?</u> <br/> 자식 컴포넌트 어디에서 부르든지 상관없이 동일한 포지션으로 메세지창이 나타날 수 있게 통일하기 위해서

```html
<!-- Vuetify 사용할 경우 가장 최상위 컴포넌트 -->
<v-app>


  <!-- 1. Alert 스타일 -->
  <global-alert
    :globalAlert.sync="globalAlert.show"
    @click:cxl="globalAlert.show = false"
  >
    <template>
      <div v-html="globalAlert.msg"></div>
    </template>
  </global-alert>



  <!-- 2. Confirm 스타일 -->
  <global-comfirm
    :globalConfirm.sync="globalConfirm.show"
    @click:cxl="globalConfim.show = false"
    @click:apply="globalConfirm.apply"
  >
  <template>
    <div v-html="globalConfirm.msg"></div>
  </template>
  </global-comfirm>



</v-app>

<script>
  import { mapGetters } from 'vuex';
  import GlobalAlert from '@/components/dialog/GlobalAlert.vue';
  import GlobalConfirm from '@/components/dialog/GlobalConfirm.vue';

  export default {
    computed: {
      // 전역변수는 computed에서 소환
      ...mapGetters({
        globalAlert: 'misc/globalAlert', // misc 모듈
        globalConfirm: 'misc/globalConfirm',
      }),
    },
  }
</script>
```

<br/>

### 4) Global Message를 띄우고 싶은 자식 컴포넌트 아무데나
```html
<v-container>
  ...
  <!-- if, 1번 버튼을 누르면 Alert이 열림 -->
  <v-btn @click="alertBtn"> 버튼 1 </v-btn>

  <!-- if, 2번 버튼을 누르면 Confirm이 열림 -->
  <v-btn @click="confirmBtn"> 버튼 2 </v-btn>

  <!-- if, 3번 버튼을 누르면 Confirm이 열리고, 확인을 누르면 Alert이 열림 -->
  <v-btn @click="confirmAlertBtn"> 버튼 3 </v-btn>

</v-container>
<script>
import { mapMutaions } from 'vuex';
  export default {
    methods: {
      ...mapMutations({
        globalAlert: 'misc/globalAlert',
        globalConfirm: 'misc/globalConfirm',
      }),
    },

    // 1번 버튼
    alertBtn() {
      // cxl 누르지 않아도 3초 있다가 사라짐
      this.globalAlert({
        show: true,
        msg: '1번 버튼 메세지입니다',
      });
    },

    // 2번 버튼
    confirmBtn() {
      this.globalConfirm({
        show: true,
        msg: `
        <div style="color: red"> 2번 버튼 메세지 입니다. </div>
        <h2> html 코드로 작성할 수 있어요 </h2>
        `,
        // 콜백함수 안에서 자기 자신을 소환해서 show를 false로 만듦.
        apply: () => {
          this.globalConfirm({
            show: false,
            msg: null,
            apply: () => {},
          });
        }
      });
    },

    // 3번 버튼
    confirmAlertBtn() {
      this.globalConfirm({
        show: true,
        msg: '3번 버튼 메세지입니다',
        apply: () => {
          // 자기 자신 init
          this.globalConfirm({
            show: false,
            msg: null,
            apply: () => {}
          });
        // Alert 메세지 띄움
        this.globalAlert({
          show: true,
          msg: '3번 버튼의 Alert 메세지 입니다.',
        });
        }
      });
    },
  }
</script>
```

엄청 길어보이는데 사실 상, 전역변수 활용만 잘 하면 그만이었다... :cry: <br/>

`this.globalAlert({ show: true, msg: '메세지' })` 변수를 이렇게 커스텀해서 원하는 형태로 전역메세지를 설정할 수 있다는 걸 왜 이제 알았지..?


<br/><br/>
