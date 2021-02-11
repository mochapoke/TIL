# 문제
[리액트공식문서](https://ko.reactjs.org/docs/lists-and-keys.html)

리액트의 map method를 이용하면서 가장 자주 부딛히는 문제가 바로 unique key를 생성하는게 아닐까 싶다.
물런 DB에서 가져온 데이터는 데이터마다 달린 id가 있기때문에 상관 없다.
내가 해결하고자 하는 문제는, 사용자가 생성한 데이터를 DB에 전송하지않고 클라이언트 웹상에서만 표현하고 말 것들이다.

## unique key를 써야하는 이유는 뭘까? 🤔
바로 리액트의 특유의 dom 업데이트 방식때문인데, 
상태가 변할 때 마다 unique key로 이전과 달라진 화면이 무엇인지 비교해서 특정 key의 컴포넌트만 새롭게 렌더한다.
그렇기 때문에 map의 index로만 key를 설정해주면, 똑같은 id(즉, 1, 2, 3, 4...)가 중첩이 되기 때문에 좋지 않다!
게다가 배열의 아이템 중 하나를 삭제하면 index가 엉켜버리기 때문에 고유하지가 않다.
> map의 index를 key로 줬을 때,
key가 1, 2 인 배열이 있다고 하자. 여기서 key가 1인 아이템을 삭제하면, key가 2였던 애가 1로 변경이 된다. 😨

> Key는 배열 안에서 형제 사이에서 고유해야 하고 전체 범위에서 고유할 필요는 없습니다. 두 개의 다른 배열을 만들 때 동일한 key를 사용할 수 있습니다.


## 생성 방법
방법은 단순하다. 매번 새로운 문자를 만들면 된다. 😂

`Math.random` 은 매번 랜덤한 숫자를 생성해낸다
>0.15548033496669555

`.toString(36)`을 이용하여 적당한 알파벳과 숫자의 글자로 변경한다. 
36이 아니고 16이어도 상관없다.
그 결과는 아래와 같다.
>0.bak9ijujf8a

그 후, `.substr(2,6)`을 이용해서 처음 2글자를 제외한 6글자를 return한다
>oa37r4

`.substring`과 혼돈하지말자. `substring(2,6)` 이면 단어의 2번째와 6번째 글자 사이, 즉 4글자를 return한다.
4글자여도 충분히 unique Id의 기능을 수행하지만, substr이 substring보다 글자가 짧으니깐..😉 게다가 인식하기도 쉽다!

## 사용 예제

일기장을 예시로 든 아주 간단한 예제이다.

```js
const [diaries, setDiaries] = useState([]);

const updateData = () =>{
    const newDiary = {
      id: Math.random().toString(36).substr(2, 4),
      title: title,
      content: content,
    };

    setDiaries([...diaries, newDiary]);
  };
  
return (
  <>
    {diaries.map((diary) => (
      <div key={diary.id}>
        <span>{diary.title}</span>
        <p>{diary.content}</p>
      </div>
    )}
  </>
)
```



## 한계
유저가 생성한 데이터에 추가로 unique id를 매번 만드는 방식이다 보니, 이미 존재하는 데이터 배열을 mapping할 때는 사용할 수가 없다.
하지만, 이 한계를 해결하는 npm 라이브러리가 존재하므로 참고할 것!
