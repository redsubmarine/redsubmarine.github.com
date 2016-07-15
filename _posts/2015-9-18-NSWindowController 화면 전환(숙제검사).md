---
layout: post
title: NSWindowController(숙제검사)
---

### NSWindowController 화면 전환

Cocoa 기반의 프로젝트를 만든다.

NSWindowController 를 상속받는 MyWindowController 를 만들고, MyWindowController.xib 에 화면을 구성하자.

이 화면은 ViewController 에서 버튼을 누르면 호출되는 화면이다. Main.storyboard 에서 ViewController 에 버튼을 하나 올리고, ViewController.m 파일에 IBAction을 걸어준다.

	- (IBAction)showWindow:(id)sender {
    	_windowController = [[MyWindowController alloc] initWithWindowNibName:@"MyWindowController"];
    	[_windowController showWindow:nil];
    }

이렇게만 해주면 새로운 윈도우가 뜨는 것을 확인할 수 있다.


앱을 만들다보면 이렇게 윈도우를 띄우는 경우도 있지만, sheetStyle로 떠야 할 때가 있다.

그럴 경우는,

	- (IBAction)showWindow:(id)sender {
		_windowController = [[MyWindowController alloc] initWithWindowNibName:@"MyWindowController"];
		[[NSApp mainWindow] beginSheet:_windowController.window completionHandler:nil];
	}
	
이렇게 작성해 주면 된다. 하지만 이렇게 Sheet 로 띄울 경우 닫기 버튼을 따로 만들어주지 않으면, 윈도우를 닫지도 못할뿐더러, 앱을 종료조차 할수 없게 된다.