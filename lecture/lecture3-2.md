카메라/배경 설정  
=======================
![lecture3-2-1](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-1.PNG)
* 맵을 만들다 보니까 문제가 생겼다.  
맵은 더 넓어졌는데 카메라는 고정이여서 내 캐릭터가 카메라 밖으로 나가서 안보인다.  
어떻게 해야될까?  
--------------------------  
![lecture3-2-2](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-2.PNG)
![lecture3-2-3](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-3.PNG)  
* 그냥 메인 카메라를 캐릭터에 종속시켜버리면 알아서 따라오지 않을까?  
하지만 캐릭터가 좌우 방향으로 움직일때마다 카메라 위치가 180도 변해서     
내 캐릭터는 오른쪽만 보고 세상이 바뀌는 기괴한 게임이 된다.      
--------------------------------------------------------      
![lecture3-2-5](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-5.jpg)      
* 그럼 어떻게 해야 카메라가 캐릭터를 계속 비추게 할 수 있을까?  
-------------------------------  
``` 
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class camera : MonoBehaviour
{
    public Transform player;//따라다닐 player지정  
    void Update()
    {
        gameObject.transform.position = new Vector3(player.position.x, player.position.y, this.transform.position.z);//카메라가 player를 따라다니게 한다.  
        //카메라의 z위치는 비추려는 대상보다 뒤에있어야 하니까 자체적으로 z값을 준다.
    }
}

```  
* 간단하게 카메라가 player gameObject를 따라다니게 하면 멀쩡한 카메라가 된다. 
-------------------------------------------------------  
![lecture3-2-6](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-6.png)      
* 방금 짠 코드를 maincamera에 넣어주자.  
-------------------------------  
![lecture3-2-7](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-7.png)      
* 스크립트에서 추적할 gameObject에 player를 넣어주자.  
-------------------------------  
![lecture3-2-8](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-8.PNG)      
* 실행해보면 아주 잘된다.  
-------------------------------  
![lecture3-2-9](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-9.PNG)      
* 근데 player가 어디에 있던 카메라가 날 정 가운데 비추어서 내가 어디쯤에 있는지 감이 너무 안잡힌다.  
이러면 어떻게 해야될까?  
-------------------------------  
![lecture3-2-10](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-10.PNG)      
* 유니티의 mathf.clamp 기능을 써주면 최대값과 최솟값을 정해줄 수 있다.   
-------------------------------  
```  
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
public class camera : MonoBehaviour
{
    public Transform player;
    public float xMin;
    public float yMin;
    public float xMax;
    public float yMax;
    void Update()
    {
        float x = Mathf.Clamp(player.position.x, xMin, xMax);//어떤 값의 최대값과 최솟값을 정해주는 함수.
        float y = Mathf.Clamp(player.position.y, yMin, yMax);
        gameObject.transform.position = new Vector3(x, y,this.transform.position.z);
        //카메라의 z위치는 비추려는 대상보다 뒤에있어야 하니까 자체적으로 z값을 준다.
    }

}
```  
* mathf.clamp를 사용하여 코드를 짜주자.  
---------------------------------------------------------
![lecture3-2-11](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-11.PNG)      
* 카메라가 비추는 x,y좌표의 최소, 최대값을 정해준다.  
-------------------------------  
![lecture3-2-12](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-12.PNG)      
* 실행해보면 아까와는 다르게 위치감이 느껴진다.  
-------------------------------    
![lecture3-2-13](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-13.png)      
* 카메라에 사용한 코드 그대로 배경에도 쓸 수 있다.  
배경으로 쓸 이미지를 드래그&드롭 해주자.  
-------------------------------    
![lecture3-2-14](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-14.png)      
* 배경이 타일맵을 가려버렸다. 어떻게 해야될까?   
* 다시 add layer로 가자.  
-------------------------------    
![lecture3-2-15](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-15.PNG)      
* sorting layers에서 background를 추가해주자.  
(제일먼저 앞에 보이고 싶은 layer일수록 아래에 위치한다.)  
-------------------------------    
![lecture3-2-16](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-16.PNG)      
* 배경의 크기와 sorting layer, 그리고 아까짠 코드의 설정을 해준다.  
-------------------------------    
![lecture3-2-17](https://github.com/isp829/HU/blob/master/images/lecture3/3-2/3-2-17.PNG)      
* 실행해보면 캐릭터가 움직여 맵이 변해도 배경은 변하지 않아 원근감이 느껴진다.   
-------------------------------    
[목차로](https://github.com/isp829/HU/blob/master/README.md)  
[다음](https://github.com/isp829/HU/blob/master/lecture/lecture3-3.md)   
-----------------------------  
