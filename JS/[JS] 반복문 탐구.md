# for 문법 종류에 따른 효율성 체크
## 개요
> 프로그래머스 lv.0 전국 대회 선발 고사 문제를 풀다가 for (var i in arr)를 사용하면 i 가 스트링으로 변환된다는 것을 알게됨. 배열을 도는 i는 key값을 받아오는데 key에는 symbol 아니면 string밖에 못들어가기 때문. 

> 그래서 i를 갖다가 계산을 해야 하는 수식이 있어서 parseInt()를 두 번이나 돌려야 했다. parseInt()를 제거하면 효율성이 높아질 것이라고 생각해서 for in문 대신 for i=0 반복문을 사용했는데 같거나 오히려 늘어나는경우도 있었다. 원인이 궁금해서 알아보고자 한다!

## for in 문 + parseInt() 사용
```function solution(rank, attendance) {
    var answer = [];
    let arr = [];
    
    // 참가 여부 가려내기
    for (var i in rank ) {
        if(attendance[i]){
            let student = {
                studentN : i, 
                rank : rank[i],
                };
            arr.push(student);
        }};
    
    //rank 순으로 정렬
    arr.sort((a,b)=>a.rank-b.rank);
    //10000 * a + 100*b + c
    answer = arr[0].studentN * 10000 + parseInt(arr[1].studentN) * 100 + parseInt(arr[2].studentN);    
    
    return answer;
}
```
## for i=0 문 + parseInt() 제거
```function solution(rank, attendance) {
    var answer = [];
    let arr = [];
    
    // 참가 여부 가려내기
    for (let i=0; i<rank.length; i++ ) {
        if(attendance[i]){
            let student = {
                studentN : i, 
                rank : rank[i],
                };
            arr.push(student);
        }};
    
    //rank 순으로 정렬
    arr.sort((a,b)=>a.rank-b.rank);
    console.log(isNaN(arr[1].studentN));
    //10000 * a + 100*b + c
    answer = arr[0].studentN * 10000 + arr[1].studentN * 100 + arr[2].studentN;    
    
    return answer;
}
```

라랄ㄹ랄