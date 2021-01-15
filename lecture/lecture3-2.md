메뉴 만들기 
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-2.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-3.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-4.PNG" width="50%">  

* 각각의 메뉴들에 menu를 달아주고 이름을 붙여준다.  
* 맨처음에는 loadingMenu가 보여야 되니까 open에 체크해준다.  
* 캔버스에는 menuManager를 달아주고 menu들을 넣어준다.  
---------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-1.PNG" width="50%">  

* launcher에서 로비에 들어가면 타이틀 메뉴가 켜지도록 코드를 수정해준다.
---------------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;//포톤 기능 사용

public class Launcher : MonoBehaviourPunCallbacks//다른 포톤 반응 받아들이기
{
    void Start()
    {
        Debug.Log("Connecting to Master");
        PhotonNetwork.ConnectUsingSettings();//설정한 포톤 서버에 때라 마스터 서버에 연결
    }

    public override void OnConnectedToMaster()//마스터서버에 연결시 작동됨
    {
        Debug.Log("Connected to Master");
        PhotonNetwork.JoinLobby();//마스터 서버 연결시 로비로 연결
    }

    public override void OnJoinedLobby()//로비에 연결시 작동
    {
        MenuManager.Instance.OpenMenu("title");//로비에 들어오면 타이틀 메뉴 키기
        Debug.Log("Joined Lobby");
    }
    void Update()
    {
        
    }
}

```

* 수정한 코드의 전문이다.

----------------------- 
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-5.PNG" width="50%">  

* 맨처음 실행할때는 loadingMenu말고는 안떠야 하므로 비활성화 시켜주자. 
---------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-6.PNG" width="50%">  

* 실행해보면 loading이 뜨다가 title메뉴가 뜨는걸 확일할 수 있다.  
---------------------------------------   
[목차로](https://github.com/isp829/3dunitymulty/blob/master/README.md)  
[다음](https://github.com/isp829/3dunitymulty/blob/master/lecture/lecture3-3.md)  
-----------------------------
