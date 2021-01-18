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
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-7.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-8.png" width="50%">  

* create empty로 create room menu를 만들어주고 텍스트 메쉬 프로 input field를 넣어준다. 

---------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-9.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-10.png" width="50%">  

* 창 크기 조절과 글자 크기 조절을 해준다. 

---------------------------------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-11.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-12.PNG" width="50%">  

* place holder의 내용도 바꿔준다.  

---------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-13.PNG" width="50%">  

* game창으로 보면 이런 상태이다.

---------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-14.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-15.PNG" width="50%">  

* title menu에서 사용했던 버튼을 복사해서 붙여넣기 해주고 항목들의 이름을 정리해주자. 

---------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-16.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-17.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-18.PNG" width="50%">  

* launcher 스크립트를 열어서 수정해주자. 

---------------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;//포톤 기능 사용
using TMPro;//텍스트 메쉬 프로 기능 사용

public class Launcher : MonoBehaviourPunCallbacks//다른 포톤 반응 받아들이기
{
    [SerializeField] TMP_InputField roomNameInputField;
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
    public void CreateRoom()//방만들기
    {
        if (string.IsNullOrEmpty(roomNameInputField.text))
        {
            return;//방 이름이 빈값이면 방 안만들어짐
        }
        PhotonNetwork.CreateRoom(roomNameInputField.text);//포톤 네트워크기능으로 roomNameInputField.text의 이름으로 방을 만든다.
        MenuManager.Instance.OpenMenu("loading");//로딩창 열기
    }
}

```

* 수정한 launcher 스크립트이다. 

---------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-19.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-2/3-2-20.PNG" width="50%">  

* create room menu에 menu스크립트를 넣어주고 이름을 입력해준다.  
* canvas에서도 새로 입력한 요소들을 넣어준다. 

---------------------------------------   
[목차로](https://github.com/isp829/3dunitymulty/blob/master/README.md)  
[다음](https://github.com/isp829/3dunitymulty/blob/master/lecture/lecture3-3.md)  
-----------------------------
