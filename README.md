# smart-contract vulnerability report

DVP-2019-29361 An overflow vulnerability
https://dvpnet.io/lddt

Ref) https://etherscan.io/bytecode-decompiler?a=0x2f948fc70ea675c27b34ad0a2f82d321a9a50c89#

**ID** : ksil                    **Company** : everiToken

**Summary)**
The everiToken, an Ethereum token, has an integer overflow that allows the owner of the contract to set the balance of an arbitrary user to any value.

![캡처](https://user-images.githubusercontent.com/31252686/65886096-ac049a00-e3d6-11e9-9a2f-366f6e5ee20e.PNG)

![캡처1](https://user-images.githubusercontent.com/31252686/65886131-bd4da680-e3d6-11e9-8aa1-15ef57e1b782.PNG)

전통적인 취약점으로써, 값이 주어진 자료형에서 표현할 수 있는 크기를 벗어나는 경우 발생하게 된다.
일반적인 Program 에서는 치명적이지 않은 버그이지만, Ether를 다루는 Solidity 내에서 조건문이나 Balance Check에서 Over / Underflow 는 치명적인 버그로 작용한다.

중요한 관점은 _arg은 당연히 256 비트의 한계를 못 넘는 거지만, balanceOf[_arg0] 여기의 값이 256 비트에 매핑되어 있는 데 지속적으로 덧셈을 하다가 balanceOf의 256 비트의 한계치를 넘어서면 오버플로우가 나는게 포인트이다.

결론적으로 덧셈을 한 결과값에 대한 check 문이 빠져 있는 셈이다.
balanceOf[_arg0] + _value > balanceOf[_arg0] 이러한 조건이 없다는 것과 동일하다. 덧셈을 했을 때 결국에 오버플로우가 터지면 0 값과 가까운 값으로 수렴하니까 덧셈을 하여도 결과값이 _value 값보다 작게 되는 결과를 초래한다.

사실은 치명적으로 공격을 할 수 있는 어택이라기보다는 서비스 과정에서 256 비트를 넘어서면 서비스가 불합리하게 흘러갈 수 있는 버그(Logic bug)이다.


**Solutions)**
SafeMath Library 사용
Condition Check
