메뉴 만들기
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-1.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-2.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-3.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-4.PNG" width="50%">  

* Room Menu를 만들어주고 그안에 텍스트 메쉬프로를 넣어준다.  
* 크기와 위치들을 조정해준다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-5.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-6.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-7.PNG" width="50%">  

* Create room에 있는 버튼을 복사 붙여넣기 해준다.  
* 크기와 위치 조정을 해주고 버튼에 글자를 수정해준다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-8.PNG" width="50%">  

* game창에서 보면 이런 상태로 보인다.  

------------------------------------------------------------- 
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-9.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-10.PNG" width="50%">  

* menu스크립트를 넣어주고 이름을 넣어준다.   
* canvas에 추가한 메뉴를 넣어준다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-11.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-12.PNG" width="50%">  

* room menu를 복사 붙여넣기 해서 error메뉴를 만들어준다.  
* 텍스트들을 수정해준다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-13.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-14.PNG" width="50%">  

* menu스크립트를 넣어주고 이름을 넣어준다.   
* canvas에 추가한 메뉴를 넣어준다. 

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-15.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-16.PNG" width="50%">  

* launcher 스크립트를 열어서 room메뉴와 error메뉴 스크립트를 작성해준다.

-------------------------------------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;//포톤 기능 사용
using TMPro;//텍스트 메쉬 프로 기능 사용

public class Launcher : MonoBehaviourPunCallbacks//다른 포톤 반응 받아들이기
{
    [SerializeField] TMP_InputField roomNameInputField;
    [SerializeField] TMP_Text errorText;
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

    public override void OnJoinedRoom()//방에 들어갔을때 작동
    {
        MenuManager.Instance.OpenMenu("room");//룸 메뉴 열기
    }

    public override void OnCreateRoomFailed(short returnCode, string message)//방 만들기 실패시 작동
    {
        errorText.text = "Room Creation Failed: " + message;
        MenuManager.Instance.OpenMenu("error");//에러 메뉴 열기
    }
}
```

* 수정한 코드의 전문이다. 

------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-17.png" width="50%">  

* canvas에 수정한 스크립트 요소들을 추가해 준다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-18.PNG" width="33%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-19.PNG" width="33%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-3/3-3-20.PNG" width="33%">

* 만들어준 버튼들을 눌렀을때 어떤 행동을 해야 할지 생각하면서 눌렀을때 행동들을 다 정해준다.  

---------------------------------------------  
[목차로](https://github.com/isp829/3dunitymulty/blob/master/README.md)  
[다음](https://github.com/isp829/3dunitymulty/blob/master/lecture/lecture3-4.md)  
-----------------------------
