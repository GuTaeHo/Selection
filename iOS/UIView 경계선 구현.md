## UIView 경계선 위 UIView 구현

**borderWidth**
When this value is greater than 0.0, the layer draws a border using the current borderColor value. 
The border is drawn inset from the receiver’s bounds by the value specified in this property. 
It is composited above the receiver’s contents and sublayers and includes the effects of the cornerRadius property.
The default value of this property is 0.0.

해당값이 0보다 크면, 현재 테두리 색에 따라 경계선을 그린다.
이 속성의 지정된 값에 의해 수신자(해당 뷰)의 bounds 에 맞게 경계선이 삽입되어 그려진다.
수신자의 컨텐츠와 sublayer 들과 합성되며, cornerRaduis 속성의 효과를 포함한다.
기본 값은 0이다.

</br>

**수신자의 컨텐츠와 sublayer 들과 합성되며...**
ParentView 와 SubView 가 있고, ParentView 에 경계선이 표시되고 있을 때, 
SubView가 경계선과 동일한 위치에 있는 경우 경계선이 항상 SubView 위를 지나감

해결 방법

1. 베지어 패스(UIBezierPath)를 이용하여 직접 뷰모뷰의 경계면을 따라 선을 그리기
2. 뷰의 배경이될 상위 뷰를 하나 넣고 현재 뷰를 조금 작게 그려 **경계선처럼** 보이도록 표시