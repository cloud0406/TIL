### display: flex

- 자식 엘레먼트에는 아무것도 작성하지 않는다. (부모에만 작성)
- justify-content는 메인 축(main axis)을, align-items는 교차 축 (cross axis)을 설정
- flex-direction : column
  - 주축을 수직으로 변경
- flex-direction: row-reverse
  - 주축 수평, 역순으로 배열
- flex는 바로 아래 자식한테만 적용
  - 자식의 자식한테는 적용 x
  - 부모의 부모가 flex를 가지고 있어도 justify-content등 속성 적용 안됨)
- 속성들
  - center,
  - space-between
  - flex-start, flex-end
- vh는 view point -> 100vh는 화면 높이의 100%
