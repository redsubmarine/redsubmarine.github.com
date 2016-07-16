---
layout: post
title: 흔히 쓰던 NSNotificationCenter 에 관한 오개념 정리
---

나만 잘못알고 있었던건지는 모르겠지만 매서드 이름만으로 넘겨짚어 짰던 코드의 문제점을 이제서나마 알게 되어 포스팅 한다. NotificationCenter 에 Observer 로서 객체를 추가하는 코드는 다음과 같다.

	override func viewDidLoad() {
	super.viewDidLoad()
		NSNotificationCenter.defaultCenter().addObserver(self, selector: #selector(observerAction), name: "ObserverMe", object: nil)
	}
	​
	func observerAction(notification: NSNotification) {
		print("handle notification")
	}
	​
	deinit {
		NSNotificationCenter.defaultCenter().removeObserver(self)
	}


iOS 2.0 에서부터 주어진 addObserver:selector:name:object: 라는 매서드는 이렇게 사용했다. 상황에 따라 removeObserver:name:object: 로 이름까지 찾아서 제거할 수도 있지만, 위처럼 self 를 제거하면 self 에 대한 모든 observer가 제거 된다. 그런데 iOS4.0 에서 블럭구문을 지원하면서 부터 하나의 매서드가 더 생겼다. 바로 addObserverForName:object:queue:usingBlock: 이 매서드다.

	override func viewDidLoad() {
		super.viewDidLoad()
	​
		NSNotificationCenter.defaultCenter().addObserverForName("ObserverMe", object: nil, queue: NSOperationQueue.mainQueue()) { notification in
		print("handle notification")
		}
	}
	​
	deinit {
		NSNotificationCenter.defaultCenter().removeObserver(self)
	}

필자는 앞의 것과 크게 다르게 생각하지 않고 이렇게 사용하고 있었다. 하지만 이 코드에는 큰 문제점이 있다. 해당 뷰컨트롤러를 나갔다가 다시 들어온다거나 해서 "ObserverMe" 라는 이름으로 노티피케이션을 post 해보면.... ​*handle notification*​이 두번찍히는걸 볼 수 있다. 그러면 뷰컨트롤러가 해지가 안된것인가? 그것은 아니다. deinit 에서 로그를 찍어보면 정상적으로 호출되는 것을 확인할수 있다. 문제는 위에 두 코드를 보면 놓치고 지나갔던 부분을 볼 수 있다. 먼저 첫번째 코드, 그러니까 셀렉터를 사용했던 코드에서는 매서드 이름의 addObserver 이며 그 observer로서 self 를 추가하고 있다. 하지만, 두번째 코드에서는 단지 이름으로만 추가를 하고 있다. self 를 추가하지 않기 때문에 deinit 에서 self 를 제거해봤자, 추가된 observer는 해제되지 않는 것이다. 그렇다면 어떻게 해야 observer를 해제할 수 있는가!? 

addObserverForName:object:queue:usingBlock: 매서드의 정의를 따라가보면 return 값으로 NSObjectProtocol 을 리턴하는 것을 볼 수 있다.
> func addObserverForName(_ name: String?, object obj: AnyObject?, queue queue: NSOperationQueue?, usingBlock block: (NSNotification) -\> Void) -\> NSObjectProtocol


그렇다. 이 객체가 observer로서 등록이 된것이고, 이 객체를 해제해야 정상적으로 동작을 한다.

	var observers = [NSObjectProtocol]()
	​
	override func viewDidLoad() {
		super.viewDidLoad()
	​
		let observer1 = NSNotificationCenter.defaultCenter().addObserverForName("ObserverMe", object: nil, queue: NSOperationQueue.mainQueue()) { notification in
			print("handle notification")
		}
		observers.append(observer1)
	}
	​
	deinit {
		_ = observers.map { object in
		NSNotificationCenter.defaultCenter().removeObserver(object)
		}
	}

최종 코드는 위와 같다. 특히, deinit 에서 map 을 사용해서 코드가 좀 더 깔끔해진 느낌이다.
