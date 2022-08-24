---
layout: post
title: "🍮Jellymodi"
info: "머신러닝 사물인식 프로젝트"
tech: "python, flask, mongoDB, ajax, OpenCV, deeplearning"
type: 내일배움캠프
---

![logo](https://user-images.githubusercontent.com/104331479/185329655-95f41df4-dec5-4e94-b6b8-471a0ef2deba.png)

<br/>

# Jellymodi :Jelly mood diary
---
사진 속 표정에 맞는 이모티콘을 찾아주어 하루의 감정을 쉽게 기록할 수 있는 일기 작성 서비스  

[`🔗시연영상`](https://tv.kakao.com/v/429054454)  

## 주요 기능
1. JWT를 이용한 로그인
1. 사용자가 업로드한 사진에서 OpenCV를 이용해 얼굴을 인식하고, 표정 데이터를 학습시킨 모델을 통해 `표정을 판별`하여 해당하는 감정 이모티콘으로 자동 변환
1. 젤리모디만의 귀여운 감정 표현 이모티콘
    <details>
    <summary>젤리티콘</summary>
    <div markdown="1">

    * 각 감정의 2번 이모티콘 제작 담당

    ![젤리티콘](https://user-images.githubusercontent.com/104331479/185370310-7f77facf-19a5-43e2-8b67-f8bc4e62302d.PNG)

    </div>
    </details>
1. MongoDB Atlas를 이용한 일기 CRUD
1. 이모티콘을 월별로 묶어 리스트로 보여주어 나의 감정 추이를 시각적으로 확인
1. Midea Query를 이용한 반응형 페이지

<br/>

# Project
---
* [`🔗Github`](https://github.com/cmjcum/Jellymodi_team)
* [`🔗Starting Assignment (figma)`](https://www.figma.com/file/2Cly7HHq84Poz0s6gWyv46/%EC%BD%94%EB%AA%BB%EC%A3%BD---%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%82%AC%EB%AC%BC%EC%9D%B8%EC%8B%9D?node-id=0%3A1)

## 프로젝트 기간
2022-05-18 ~ 2022-05-25

<br/>

## 팀 구성 및 역할

| 이름 | 역할 | 깃허브 |
|:----------|:----------|:----------:|
| **이정아&nbsp;&nbsp;&nbsp;&nbsp;** | 일기 조회 페이지 | [`🔗zeonga1102`](https://github.com/zeonga1102)|
| **노을🌱** | 메인 페이지, 로고 제작 | [`🔗minkkky`](https://github.com/minkkky) |
| **이현경** | 로그인 및 회원가입 페이지&nbsp;&nbsp;&nbsp;&nbsp; | [`🔗LULULALA2`](https://github.com/LULULALA2)|
| **김동근** | 일기 작성 페이지 | [`🔗yinmsk`](https://github.com/yinmsk) |

* 모델제작 - 팀 전체 참여

<br/>

## 스킬 및 사용툴
`Python`, `Flask`, `Blueprint`, `MongoDB`, `Ajax`, `OpenCV`, `Machine Learning`

<br/>

## 나의 역할

### 한국인 얼굴 표정 데이터를 학습시킨 머신러닝 사물인식 모델 제작
1. 데이터셋 : AIHub - [한국인 감정인식을 위한 복합 영상](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=82)
1. 데이터셋 전처리 과정
    1. `Problem` 데이터셋의 용량이 너무 큼  
        (원천 데이터 50만 건, 800GB)
        * `Action` 데이터셋의 Validation 내의 원천 데이터로 한정
    1. `Problem` 감정의 종류가 너무 다양하여 학습 결과의 정확도를 올리기 힘듦  
        (기쁨, 당황, 분노, 불안, 상처, 슬픔, 중립)
        * `Action` 기쁨, 분노, 슬픔, 중립 4가지 감정만 사용
    1. 기쁨 감정의 데이터 중 표정이 뚜렷한 데이터를 약 1천 건 선별
    1. 인식률을 높이기 위해 얼굴만 크롭하여 이미지를 가공
1. Colab에서 팀원들이 각자 모델을 제작한 후 가장 감정 인식률이 높은 모델 채택 [`🔗colab`](https://colab.research.google.com/drive/1ZfSkro5jsiXD6q_FnazaJv5CHzoJFHtH?usp=sharing)

### 메인 페이지의 백엔드  
1. `Goal` 해당 이용자가 작성한 일기를 월별로 불러오기
1. 가장 최근에 글을 쓴 달부터 글을 표시하기 위해서 데이터를 DB에서 불러올 때 내림차순으로 sort하여 가져왔다.
```python
posts = list(db.posts.find({"email": payload['email']}).sort('date', -1))
```
1. `Problem` 월별로 데이터를 구분하여 출력할 수 없었다.
1. `Action`  for문과 try문을 이용하여 데이터를 월별로 묶어주었다.
    ```python
    temp = {}
    for post in posts:
        try:
            temp[post['date'].strftime('%Y %B')].append(post)
        except: # 작성된 글이 없을 때
            temp[post['date'].strftime('%Y %B')] = [post]

    return render_template('index.html', temp=temp)
    ```
1. `Problem` 월별로 내림차순 정렬된 것은 좋았으나, 일별로도 내림차순 정렬되어 원하는 형태가 아니었다.
1. `Action`   date에서 년월/날짜 값을 뽑아낸 후 sort()를 이용하여 년월 값으로는 내림차순 정렬을 하고, 날짜 값으로는 오름차순 정렬을 하는 `다중조건`으로 정렬하여 해결했다.
    ```python
    posts = list(db.posts.find({"email": payload['email']}))

    for post in posts:
        post['Y-M'] = post['date'].strftime('%Y%m') # 'Y-M': yyyymm
        post['day'] = post['date'].strftime('%d') # 'day': dd

    posts.sort(key=lambda x: (-int(x['Y-M']), x['day']))
    ```

### 메인 페이지의 프론트엔드
1. `Goal` jinja2 문법을 사용하여 데이터 출력하기
1. `Problem` 서버에서 받아온 데이터는 딕셔너리와 리스트가 복합된 형태로, key값과 item값을 둘 다 사용하는 방법을 몰랐다.
1. `Action` for문과 `items()` 함수를 사용하여 출력했다.
    {%  raw %}
    ```html
    {% for key, value in temp.items() %}
    <div class="list">
        <h5><span class="deco">{{ key }}<span class="small">  (젤리 {{ value | length }}개)</span></span></h5>
        <div class="jelly_box">
            {% for val in value %}
                <span data_tooltip="{{ val.date.strftime('%d %a') }}">
                    <a href={{url_for('detail.show_detail', post_id=val._id)}}>
                        <div class="jelly"><img src={{val.emoji}}></div>
                    </a>
                </span>
            {% endfor %}
        </div>
    </div>
    {% endfor %}
    ```
    {% endraw %}

<br/>

## 회고
>기획 단계에서 브레인스토밍으로 나온 아이디어들을 잘 활용해서 하나의 서비스로 구상한 것을 생각했던 대로 기획안과 컨셉에 맞춰서 충실하게 디자인과 기능을 구현해서 아주 만족스럽다.  

>머신러닝 모델 제작 부분은 전처리부터 모델 학습시키는 것까지 팀원 전부가 참여한 점과 이 과정을 직접 경험해본 점도 좋았다.  

>머신러닝 모델을 학습시키는 데에서 그치지 않고 실제로 활용하여 하나의 앱에 자연스럽게 녹인 것도 좋았다. 실제로 서비스해도 될 정도의 퀄리티로 완성되어 호평을 받아 흐뭇하다.