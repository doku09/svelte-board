<script lang="ts">
  // 1. state인 count가 변하면 derived인 double도 변한다.
  let count1 = $state(0);
  let doubled1 = $derived(count1 * 2);

  //2. derived.by는 짧은 표현식으로 표현하기 어려운 복잡한 파생상태를 만든다.
	let numbers2 = $state([1, 2, 3]);
	let total2 = $derived.by(() => {
		let total2 = 0;
		for (const n of numbers2) {
			total2 += n;
		}
		return total2;
	});

  // 3. derived는 변경되지 않으면 렌더링하지 않는다. 값이 변경될때만 업데이트한다.
  let count3 = $state(0);
  let large3 = $derived(count3 > 10);

  // 4. 객체 상태에서 derived 재할당
  let person4 = {
    name:'dong',
    age:20
  }

  let flag4 = $state(false);
  let dePerson4 = $derived.by(() => {
    let tmp = person4;
    debugger;
    if(flag4) {
      tmp = {
        name:'hwang',
        age:28
      }
    }
    return tmp;
  })


</script>

<div class="testCase">
  <div class="subject"> 1. state인 count가 변하면 derived인 double도 변한다.</div>
  <div class="when"><button onclick={() => {count1++}}>count1++</button> </div>
  <div class="result">count1++:{count1}</div>
  <div class="result">doubled1:{doubled1}</div>
</div>

<div class="testCase">
  <div class="subject"> 2. derived.by는 짧은 표현식으로 표현하기 어려운 복잡한 파생상태를 만든다. </div>
  <div class="when"><button onclick={() => numbers2.push(numbers2.length + 1)}>
    numbers2.push(numbers2.length + 1)
  </button> </div>
  <div class="result">{numbers2.join(' + ')} = {total2}</div>
  <div class="result"></div>
</div>

<div class="testCase">
  <div class="subject">3. count가 10이 넘어가면 boolean값을 true로 업데이트한다.  </div>
  <div class="when"><button onclick={() => {count3++}}>count3++</button> </div>
  <div class="result">count:{count3}</div>
  <div class="result">{large3}</div>
</div>

<div class="testCase">
  <div class="subject">4. 객체 상태에서 derived 재할당 </div>
  <div class="when"><button onclick={() => {flag4 = true}}>flag4 = true</button> </div>
  <div class="when"><button onclick={() => {flag4 = false;}}>flag4 = false</button> </div>
  <div class="result">dePerson4.age = {dePerson4.age}</div>
  <div class="result">dePerson4.name = {dePerson4.name}</div>
</div>

<div class="testCase">
  <div class="subject"> </div>
  <div class="when"><button onclick={() => {}}></button> </div>
  <div class="result"></div>
  <div class="result"></div>
</div>

<div class="testCase">
  <div class="subject"> </div>
  <div class="when"><button onclick={() => {}}></button> </div>
  <div class="result"></div>
  <div class="result"></div>
</div>

<div style="padding-bottom:500px;"></div>

<style>
  .testCase {
    border: 1px solid black;
    padding: 20px 0px 20px 20px;
    margin: 30px 0px 30px 0px;
    
  }
  .subject {
    margin-bottom: 13px;
    font-weight: 700;
    color: #1a1a2e;
  }
</style>