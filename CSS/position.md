### position

- 위치를 살짝 변경할때 사용
- position: fixed
  - 초기에 위치한 자리에 항상 고정
  - 레이아웃 상에서 그려진 위치에 이후에도 항상 고정되어있음 → 스크롤 내려도 항상 그자리
  - top, left, right, bottom으로 위치 수정 가능
    - 4개중 하나만 수정해도 block, margin등을 신경쓰지 않고 절대적인 위치에 고정
- position: relative
  - 처음 위치한 곳을 기준으로 수정
  - top, left, right, bottom으로 수정
  - 처음 위치한 곳에서 설정한 값만큼 이동한 곳에 위치하게 만듬
- position: absolute
  - 가장 가까운 relative 부모를 기준으로 이동
  - absolute로 위치 이동하려면 기준점이 되는 부모에 position:relative를 설정해줘야함, 설정하지 않을 경우 최상단의 body기준으로 움직임)
