---
layout: post
title: NSButton(숙제검사)
---

Cocoa(데스크탑)와 Cocoa Touch(모바일)에서의 Button 에 대해 알아보았다. Cocoa Touch 에서 버튼은 UIButton 클래스로 만들고, Cocoa 에서는 NSButton 클래스로 만든다. 단순히 접두사만 다른 것 같은 이 두 클래스에 대해 간단히 정리해본다.

## UIButton
CocoaTouch 에서 버튼을 생성하고 매서드와 연결하는 방법은 다음과 같이 크게 두가지가 있다. 

### 1. Interface Builder(이하 IB) 에서 만들어서 연결.

스토리보드(storyboard) 또는 집(xib)파일을 열고 우측하단에 보이는 Object Library 에서 button 으로 검색하면 다음과 같이 하나의 버튼이 등장한다.

![](https://github.com/redsubmarine/redsubmarine.github.io/blob/master/images/buttonlist.png?raw=true)

 이 것을 뷰에 끌어다 놓고, 클래스에 다음과 같이, outlet과 action 을 선언해서 연결해주면 된다.

	...
	@property (weak, nonatomic) IBOulet UIButton *button;
	...
	- (IBAction)buttonAction:(id)sender;


### 2. 코드에서 직접 만들어서 매서드와 연결.

코드에서 만드는 법은 다음과 같다.

	...{
		UIButton *button = [UIButton buttonWithType: UIButtonTypeCustom];
		[button addTarget:self action:@selector(buttonAction:)];
		...//Frame 위치 조정
		[self.view addSubView:button];
	}


## NSButton
Cocoa 에서도 마찬가지의 방법을 사용한다. 하지만 전체적으로 다른부분이 꽤 존재한다. 먼저 IB 에서의 방법은 Cocoa Touch에서와 크게 다르지 않다. 다만, 그 종류가 무척 많다.(Cocoa Touch에 비해서)
Cocoa Touch 에서는 고작해야 다음과 같이 6가지 정도뿐이다.

![](https://github.com/redsubmarine/redsubmarine.github.io/blob/master/images/uibuttons.png?raw=true)

이 밖에 라디오버튼, 팝업메뉴버튼 등을 만드려면 커스텀으로 만들어줘야 한다. 반면, Cocoa 에서는 다음과 같이 종류가 10여개에 달한다.

![](https://github.com/redsubmarine/redsubmarine.github.io/blob/master/images/nsbuttons.png?raw=true)

IB에서 매서드 연결은 Cocoa Touch 에서 와 동일하다. 다음과 같은 코드를 만들어서 연결해주면 끝이다.

	@property (weak) IBOulet NSButton *button;
	...
	- (IBAction)buttonAction:(id)sender;

여기서 약간 이상한 점이 있다. Pop Up Button 을 제외한 모든 버튼이 Outlet 으로 잡을 때, NSButton 으로 잡힌다는 사실이다. 그렇다. 사실 종류가 많아보이지만 버튼의 타입이 다른 정도일뿐, NSButton 의 객체인 것이다. 다만, Radio Button의 사용은 조금 특이하다. 라디오버튼을 여러개 올리고, Action 매서드를 같은 곳으로 연결하면, 여러개 중 하나의 버튼만 선택된다.(Cocoa Touch 에서는 라디오버튼이 없을뿐더러, 여러개버튼을 이용해 커스텀으로 구현하려면 수동으로 다 해제해주어야만 한다. ㅜ ㅜ)

코드에서 직접 만들 경우는 다음과 같다.

	- (void)viewDidLoad {
    [super viewDidLoad];

    NSButton *button = [[NSButton alloc] initWithFrame:NSMakeRect(30, 30, 100, 30)];
    [button setButtonType:NSPushOnPushOffButton];
    button.title = @"Button";
    button.target = self;
    button.action = @selector(buttonAction:);
    [self.view addSubview:button];
	}
	
	- (void)buttonAction:(NSButton *)button {
	    NSLog(@"Button Clicked!");
	}
	
아무래도 Cocoa 가 먼저 만들어지고 Cocoa Touch 가 나중에 만들어진 FrameWork 라서 그 흔적을 느낄 수 있었다. 먼저 느낀 것은 ButtonTypeEnum, NSOffState, NSOnState 였다. 
Cocoa Touch 에서 UIButtonTypeEnum 은 UIButtonType을 접두어로 Custom, System, RoundedRect... 등의 타입들이 뒤에 붙는 구조지만, Cocoa에서는 

	NSMomentaryLightButton   = 0,
	NSPushOnPushOffButton    = 1,
	NSToggleButton           = 2,
	NSSwitchButton           = 3,
	NSRadioButton            = 4,
	NSMomentaryChangeButton  = 5,
	NSOnOffButton            = 6,
	NSMomentaryPushInButton  = 7,
	NSAcceleratorButton  = 8,
	NSMultiLevelAcceleratorButton  = 9,
	NSMomentaryPushButton    = 0,
	NSMomentaryLight         = 7
	
... NS만이 접두어일뿐이다. ㅜㅜ

