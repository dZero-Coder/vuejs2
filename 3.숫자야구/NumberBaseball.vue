<template>
    <div>
        <h1>{{result}}</h1>
        <!--
            v-on:submit는 @submit으로 대체가능하며, 
            e.preventDefault()도 같이 적용가능
        -->
        <form @submit.prevent="onSubmitForm">
            <input ref="answer" maxlength="4" v-model="value"/>
            <button type="submit">입력</button>
        </form>
        <div>시도: {{tries.length}}</div>
        <ul>
            <li v-for="t in tries" v-bind:key="t.try">
                <div>{{t.try}}</div>
                <div>{{t.result}}</div>
            </li>
        </ul>
    </div>
</template>

<script>
    // 화면과 관련이 없으므로 script로 분리하는게 좋음 (다른 컴포넌트에서도 사용가능)
    const getNumbers = () => {
        const candidates = [1, 2, 3, 4, 5, 6, 7, 8, 9];
        const array = [];
        for (let i = 0; i < 4; i += 1){
            const chosen = candidates.splice(Math.floor(Math.random() * (9 - i)), 1)[0];
            array.push(chosen);
        }
        return array;
    };

    export default {
        data(){
            return{
                answer: getNumbers(),       // ex) [1, 5, 3, 4]
                tries: [],
                value:'',
                result:'',
            }
        },
        methods:{
            onSubmitForm(e){
                //e.preventDefault();
                if (this.value === this.answer.join('')){   // 정답인 경우
                    this.tries.push({
                        // tries에 객체 넣기
                        try : this.value,
                        result : '홈런',
                    });
                    this.result = '홈런';

                    alert('게임을 다시 실행합니다.');
                    this.answer = getNumbers();
                    this.value = '';
                    this.tries = [];
                    this.$refs.answer.focus();
                } else{     // 정답이 틀린경우
                    if(this.tries.length >= 9){ // 10번째 시도
                        this.result=`10번 이상 틀려서 실패 답은 ${this.answer.join(',')}였습니다.`;
                    
                        alert('게임을 다시 실행합니다.');
                        this.answer = getNumbers();
                        this.value = '';
                        this.tries = [];
                        this.$refs.answer.focus();
                    }
                    let strike = 0;
                    let ball = 0;

                    // 문자열(value) -> 숫자열 배열로 변경
                    const answerArray = this.value.split('').map(v => parseInt(v));
                    for (let i = 0; i < 4; i += 1){
                        if (answerArray[i] === this.answer[i]){
                            // 숫자 자릿수 모두 정답
                            strike++;
                        }else if(this.answer.includes(answerArray[i])){
                            // 숫자만 정답
                            ball++;
                        }
                    }

                    this.tries.push({
                        // tries에 객체 넣기
                        try : this.value,
                        result : `${strike} 스트라이크, ${ball} 볼`,
                    });
                    this.value = '';
                    this.$refs.answer.focus();
                }

                
                this.value = '';
                this.$refs.answer.focus();
            }
        }
    }
</script>

<style>

</style>