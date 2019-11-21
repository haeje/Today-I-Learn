## state (true or false)를 대소문자로 표현하여 가독성을 높이는 법

어떤 상태를 boolean 값으로 테스트하는 상황을 가정하자.

    public void turnOnLoTempAlarmAtThreadshould() throws Exception{
    	hw.setTemp(WAY_TOO_COLD);
    	controller.tic();
    	assertTrue(hw.heaterState()):
    	assertTrue(hw.blowerState()):
    	assertFalse(hw.coolerState()):
    	assertFalse(hw.hiTempAlarm()):
    	assertTrue(hw.loTempAlarm()):
    }

어떤 하드웨어의 heater, bower, cooler, hi-temp-alarm, lo-temp-alarm 의 상태값을 검사하는 테스트 코드이다.

코드를 읽는 사람은 hw.~~() 함수를 보고 어떤 상태값인지 인지하고, 왼쪽의 True, False 값을 보며 테스트를 이해해야 한다. 다소 복잡한 과정을 거친다.

다음은 리팩토링 한 테스트 코드이다.

    public void turnOnLoTempAlarmAtThreadshould() throws Exception{
    	wayTooCold();
    	assertEquals("HBchL", hw.getState());
    }

위 코드에서 이해하기 힘든 코드는 함수로 감싸준다.

    hw.setTemp(WAY_TOO_COLD);
    controller.tic();

핵심은 "HBchL"로 상태를 표현한다. 대문자는 True, 소문자는 False. 

단, 각 문자가 heater, bower, cooler, hi-temp-alarm, lo-temp-alarm를 대표한다는 것을 코드를 읽는 사람들과 약속해야 한다는 단점은 있다. 하지만 가독성이 크게 증가한다.
