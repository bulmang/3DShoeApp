# 3D Shoe Animation


![%ED%99%94%EB%A9%B4_%EA%B8%B0%EB%A1%9D_2022-10-12_%EC%98%A4%ED%9B%84_10_37_41_AdobeExpress-2](https://user-images.githubusercontent.com/114594496/227913445-26f0e8ce-11df-4e71-9dad-8eb150deb930.gif)

## 앱 설명

- 나이키 신발 쇼핑 어플로서 신발 사이즈와 3D 모델링이 된 신발 이미지를 가져와서 신발을 3D로 볼 수 있게합니다.

## 앱 기능

- 신발 이미지 시점 X축 이동 , Y축 이동
- 신발 사이즈 조정
- 장바구니 버튼

## 문법

- SceneKit : 3D 이미지들을 사용하기 위해 가져오는 API
- SCNScene : 표시 가능한 3D장면을 구성하는 노드 계층 및 전역속성을 위한 컨테이너(애플  문서) / 내생각 String이나 Bool처럼 3D를 담당하는 문법
- CGFloat : 비트수의 차이 32bit에서는 float으로 64bit에서는 Double로 처리
- $변수 : 먼저 @Binding 또는 @State로 선언해준 변수들을 양방향으로 연결되도록 해줘야해서 ‘$’을 사용하여 바인디 시켜 값을 변경
- zIndex : 겹치는 뷰의 표시 순서를 제어
- func : 함수
- some : 불투명한 타입을 만들어주기 위해 사용 반환타입이 어떤것인지 알수없으니 불투명한 타입으로 선언
- Label : 레이블형식 ui의 구성요소중 하나 아이콘과 레이블의 조합을 많이 사용한다.
- let : 변수 재할당 불가 , 한번 값을 선언한 후에는 다른값으로 초기화 불가능
- ForEach(배열,id: \.slef) : 배열내에서 각 항목의 id를 정해준다 , 반복문
- horizontal : 양옆
- vertical : 위아래
- background : 뷰 뒤에 뷰를 지정
- RoundedRectangle. : 둥근 모서리 직사각형 도형
- style:.continuous :모서리를 둥글게
- matchedGeometryEffect : 움직임을 조정하고, 모든 view를 하나의 그룹으로 인식하고 ,각각 다른view라도 이view들의 위치를 기반으로 움직임의 시작점과 끝점을 계산하도록 해준다.
- @NameSpace : 화면전환 움직임을 구현하기 위한 프로퍼티 래퍼
- easeinout : 애니메이션의 한종류
- **eometryReader{ proxy in : 하위뷰들의 위치와 모양들의 정보를 알려주고 설정하는데 도움**
- .dash : 점섬을 만드는데 길이.
- dashPhase : 선이 시작되는 대시 패턴의 길이
- gesture : 탭,클릭,스위핑에서 디테일한 제스처의 상호작용
- DragGesture : 드레그 제스처를 추가
- updating : 제스처 값이 변경되면 그값이 리셋된다.
- onChange : 값이 변경될때 실행하고 싶으면 사용
- SCNTTransaction : 암시적인 에니메이션을 만들고 장면을 변경할때 업데이트 에니메이션을 제어
    - .begin : 현재 스레드에 대해 새 트랜잭션을 시작
    - .animationDuration : 현재 트랜잭션에 포함된 모든 애니메이션의 기간을 반환
    - .commit : 현재트랜잭션 중에 변경한 내용을 모두 커밋
- rootNode : 검색할 노드 계층의 루트
- ChildNodes : 하위 노드
    - recursively : 하위노드 트리의 첫번쨰 노드를 지정된이름을 반환
- UIViewRepresentable : UIView 를 생성하고 관리
- allowsCameraControl :  사용자가 장면을 렌더링 할 때 시점을 조작할수 있는지
- autoenablesDefaultLighting : Scenekit가 씬에 조명을 자동으로 추가할지 여부 결정
- antialiasingMode : 뷰의 장면을 렌더링하는 데 사용되는 엘리어스 방지모드
    - .multisampling2X : 화면 픽셀당 2개의 샘플을 사용하여 멀티샘플 안티에이리어스를 활성화

## 코드 중 일부분

```swift

import SwiftUI
import SceneKit

struct Home: View {
    
    @State var scene: SCNScene? = .init(named: "Nike_Air_Jordan.scn")
    // 뷰 속성들
    @State var isVerticalLook: Bool = false
    @State var currentSize: String = "9"
    @Namespace var animation
    
    @GestureState var offset: CGFloat = 0
    
    var body: some View {
        VStack{
            HeaderView()
            
             // 3D 미리보기
            CustomSceneView(scene: $scene)
                .frame(height: 350)
                .padding(.top, -50)
                .padding(.bottom,-15)
                .zIndex(-10)
            
            CustomSeeker()
            
            ShoePropertiesView()
        }
        .padding()
    }
    
    // 신발 속성
    @ViewBuilder
    func ShoePropertiesView()->some View{
        VStack{
            VStack(alignment: .leading,spacing: 12){
                Text("나이키 에어 조던")
                    .font(.largeTitle)
                    .fontWeight(.heavy)
                
                Text("남성 신발")
                    .fontWeight(.semibold)
                    .foregroundColor(.gray)
                
                Label {
                    Text("4.8")
                        .fontWeight(.semibold)
                } icon: {
                    Image(systemName: "star.fill")
                }
                .foregroundColor(Color("Gold"))
            }
            .padding(.top,30)
            .frame(maxWidth: .infinity,alignment: .leading)
            
            
            // 사이즈 Picker
            VStack(alignment: .leading, spacing: 12) {
                Text("크기")
                    .font(.title3.bold())
                
                ScrollView(.horizontal,showsIndicators: false) {
                    HStack(spacing: 10){
                        let sizes = ["9","9.5","10","10.5","11","11.5","12","12.5"]
                        ForEach(sizes,id: \.self){size in
                            Text(size)
                                .fontWeight(.semibold)
                                .foregroundColor(currentSize == size ? .black : .white)
                                .padding(.horizontal,20)
                                .padding(.vertical,15)
                                .background{
                                    ZStack{
                                        RoundedRectangle(cornerRadius: 10,style:.continuous)
                                            .fill(.white.opacity(0.2))

                                        if currentSize == size{
                                            RoundedRectangle(cornerRadius: 10,style:.continuous)
                                                .fill(.white)
                                                .matchedGeometryEffect(id: "TAB", in: animation)
                                        }
                                    }
                                }
                                .onTapGesture{
                                    withAnimation(.easeInOut){
                                        currentSize = size
                                    }
                                }
                              
                        }
                    }
                }
            }
            .padding(.top,20)
            
            // 버튼 체크아웃
            HStack(alignment: .top){
                Button{
                    
                } label: {
                    VStack(spacing: 12){
                        Image("Bag")
                            .resizable()
                            .renderingMode(.template)
                            .aspectRatio(contentMode: .fit)
                            .frame(width: 45, height: 45)
                        
                        Text("230,000원")
                            .fontWeight(.semibold)
                            .padding(.top, 15)
                        
                    }
                    .foregroundColor(.black)
                    .padding(10)
                    .background{
                        RoundedRectangle(cornerRadius: 20, style: .continuous)
                            .fill(.white)
                    }
                }
                
                VStack(alignment: .leading, spacing: 10){
                    Text("이 이미지는 코딩연습용 화면입니다. 신발에 대한 설명")
                        .font(.callout)
                        .fontWeight(.semibold)
                        .foregroundColor(.gray)
                    
                    Button {
                        
                    } label: {
                        Text("세부 설명")
                            .fontWeight(.semibold)
                            .foregroundColor(.white)
                    }
                    .padding(.top,10)
                    
                }
                .frame(maxWidth: .infinity, alignment: .leading)
                .padding(.leading, 20)
            }
            .padding(.top,30)
        }
        
    }
    
    // 커스텀 씨커
    @ViewBuilder
    func CustomSeeker()-> some View{
        GeometryReader{_ in
            Rectangle()
                .trim(from: 0,to: 0.474)
                .stroke(.linearGradient(colors: [
                    .clear,
                    .clear,
                    .white.opacity(0.2),
                    .white.opacity(0.6),
                    .white,
                    .white.opacity(0.6),
                    .white,
                    .white.opacity(0.2),
                    .clear,
                    .clear
                ], startPoint: .leading, endPoint: .trailing),style: StrokeStyle(lineWidth: 2,lineCap: .round,lineJoin: .round,miterLimit: 1,dash: [3], dashPhase: 1))
                .offset(x: offset)
                .overlay{
                    // Seeker 뷰
                    HStack(spacing: 3){
                        Image(systemName:  "arrowtriangle.left.fill")
                            .font(.caption)
                        
                        Image(systemName:  "arrowtriangle.right.fill")
                            .font(.caption)
                    }
                    .foregroundColor(.black)
                    .padding(.horizontal,7)
                    .padding(.vertical,10)
                    .background{
                        RoundedRectangle(cornerRadius: 10,style: .continuous)
                            .fill(.white)
                    }
                    .offset(y: -12)
                    .offset(x: offset)
                    .gesture(
                        DragGesture()
                            .updating($offset, body: { value, out, _ in
                                // 사이즈 줄이기
                                // 전체사이즈 40으로 가정
                                // 20 만큼 줄이기
                                out = value.location.x - 20
                        })
                    )
                    
                }
        }
        .frame(height: 20)
        .onChange(of: offset, perform: { newValue in
            rotateObject(animate:  offset == .zero)
        })
        .animation(.easeInOut(duration: 0.4), value: offset == .zero)
    }
    
    // 3D 객체를 회전시키기
    func rotateObject(animate:Bool = false){
        // 회전은 SwiftUI의 3D 회전과 거의 비슷합니다.
        // Y는 수평 회전에 사용되고 그 반대는 X에 사용됩니다.
        if animate{
            SCNTransaction.begin()
            SCNTransaction.animationDuration = 0.4
        }
        let newAngle = Float((offset * .pi) / 100)
        
        // 새로운 회전
        if isVerticalLook{
            scene?.rootNode.childNode(withName: "Root", recursively: true)?.eulerAngles.x = newAngle
        }else{
            scene?.rootNode.childNode(withName: "Root", recursively: true)?.eulerAngles.y = newAngle
        }
        
        if animate {
            SCNTransaction.commit()
        }
        
    }
    
    // 위에 뷰
    @ViewBuilder
    func HeaderView()->some View{
        HStack{
            Button {
                
            } label: {
                Image(systemName: "arrow.left")
                    .font(.system(size: 16,weight: .heavy))
                    .foregroundColor(.white)
                    .frame(width: 42,height: 42)
                    .background{
                        RoundedRectangle(cornerRadius: 15, style: .continuous)
                            .fill(.white.opacity(0.2))
                    }
            }
            
            Spacer()
            
            Button {
                withAnimation(.easeInOut){isVerticalLook.toggle()}
                
            } label: {
                Image(systemName: "arrow.left.and.right.righttriangle.left.righttriangle.right.fill")
                    .font(.system(size: 16,weight: .heavy))
                    .foregroundColor(.white)
                    .rotationEffect(.init(degrees: isVerticalLook ? 0 : 90))
                    .frame(width: 42,height: 42)
                    .background{
                        RoundedRectangle(cornerRadius: 15, style: .continuous)
                            .fill(.white.opacity(0.2))
                    }
            }

        }
    }
}

struct Home_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

[View](https://www.notion.so/View-42fc805da2874ef99f87c190d38ca562)
