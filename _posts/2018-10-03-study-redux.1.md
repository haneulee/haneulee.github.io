---
layout: post
title: "Redux"
date: 2018-10-03 11:03:00 +0900
categories: [Development, Redux]
tags: [javascript, react, Redux]
---

# Redux 리덕스

: 리액트를 위한 스테이트 매니지먼트 툴 (복잡한 앱에서 효율적, 단순한 앱에서 필요없음)

### Why do we need it? 왜 필요한가?

1. Components have local state, but apps have global state.
2. Sometimes state need to be shared.
3. We need to save the shared state somewhere.
4. Redux == state container

### Redux is a gloabl state container

1. Ther Redux Store is like a box
2. We go and grab what we need for the container
3. For example, from <Navigation/> we only grab the username

### Stuff to remember

1. The whole state of your app is stored in an object(store)

- state 가 복잡하고 커질 수 있어서 리덕스는 해당 오브젝트를 수정하는 것에 매우 엄격함

2. If you wanna change the data inside of this object you need to 'dispatch' (sned) an action

- 오브젝트의 데이터를 수정하는 방법은 액션을 reducer 로 보내면 가능함

3. You will send this actions to a reducer and this reducer will change the object for you.

- reducer 는 사용자를 대신해 오브젝트를 변경함

### nomad coders 강의 참고

![1](/assets/redux/1.png)  
![2](/assets/redux/2.png)  
![3](/assets/redux/3.png)  
![4](/assets/redux/4.png)  
![5](/assets/redux/5.png)  
![6](/assets/redux/6.png)  
![7](/assets/redux/7.png)
