메뉴 만들기
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-1.PNG" width="50%">  

* 프리펩의 크기를 조절해준다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-2.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-3.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-4.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-5.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-6.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-7.PNG" width="50%">    

* launcher스크립트를 열어서 코드들을 수정해준다.
* launcher를 메서드화 시켜주고 방이름표들을 만들어주고 누르면 이동하도록 만들자. 

-------------------------------------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;//포톤 기능 사용
using TMPro;//텍스트 메쉬 프로 기능 사용
using Photon.Realtime;

public class Launcher : MonoBehaviourPunCallbacks//다른 포톤 반응 받아들이기
{
    public static Launcher Instance;//Launcher스크립트를 메서드로 사용하기 위해 선언

    [SerializeField] TMP_InputField roomNameInputField;
    [SerializeField] TMP_Text errorText;
    [SerializeField] TMP_Text roomNameText;
    [SerializeField] Transform roomListContent;
    [SerializeField] GameObject roomListItemPrefab;

    void Awake()
    {
        Instance = this;//메서드로 사용
    }
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
        roomNameText.text = PhotonNetwork.CurrentRoom.Name;//들어간 방 이름표시
    }

    public override void OnCreateRoomFailed(short returnCode, string message)//방 만들기 실패시 작동
    {
        errorText.text = "Room Creation Failed: " + message;
        MenuManager.Instance.OpenMenu("error");//에러 메뉴 열기
    }

    public void LeaveRoom()
    {
        PhotonNetwork.LeaveRoom();//방떠나기 포톤 네트워크 기능
        MenuManager.Instance.OpenMenu("loading");//로딩창 열기
    }

    public void JoinRoom(RoomInfo info)
    {
        PhotonNetwork.JoinRoom(info.Name);//포톤 네트워크의 JoinRoom기능 해당이름을 가진 방으로 접속한다. 
        MenuManager.Instance.OpenMenu("loading");//로딩창 열기
    }

    public override void OnLeftRoom()//방을 떠나면 호출
    {
        MenuManager.Instance.OpenMenu("title");//방떠나기 성공시 타이틀 메뉴 호출
    }

    public override void OnRoomListUpdate(List<RoomInfo> roomList)//포톤의 룸 리스트 기능
    {
        foreach (Transform trans in roomListContent)//존재하는 모든 roomListContent
        {
            Destroy(trans.gameObject);//룸리스트 업데이트가 될때마다 싹지우기
        }
        for (int i = 0; i < roomList.Count; i++)//방갯수만큼 반복
        {
            Instantiate(roomListItemPrefab, roomListContent).GetComponent<RoomListItem>().SetUp(roomList[i]);
            //instantiate로 prefab을 roomListContent위치에 만들어주고 그 프리펩은 i번째 룸리스트가 된다. 
        }
    }
}

```

* 수정한 launcher스크립트의 전문이다. 

----------------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-8.PNG" width="50%">  

* Room List Item 스크립트를 만들어주자. 

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-9.PNG" width="50%">  

* 이름을 받아서 특정 방이름을 가진 방을 만들도록 코드를 짜주자. 

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-10.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-11.PNG" width="50%">  

* Room List Button의 프리펩으로가서 Room List Item 스크립트를 넣어주고 요소들을 넣어주자.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-12.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-13.PNG" width="50%">  

* 메뉴 스크립트를 find room menu에 넣어주고 canvas에가서 launcher에 추가한 요소들을 다 넣어주자.
* room list item prefab에는 꼭 프리펩을 넣어야된다.

----------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-14.PNG" width="50%">  

* 타이틀 메뉴에있는 find room button에 행동을 추가해준다.  

----------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-15.PNG" width="50%">  

* 실행해보면 잘된다. 

----------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-16.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-17.png" width="50%">  

* 빌드해서 테스트 하기전에 설정을 해주자. 

----------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-18.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-19.png" width="50%">  

* 설정다했으면 빌드해주자.  

----------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-20.PNG" width="33%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-21.PNG" width="33%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-5-22.PNG" width="33%">  

* 빌드한거랑 프로젝트에서 실행하는거 둘다 실행해보면 한쪽에서 만든방을 다른쪽이 볼 수 있고 참여할 수 있다.  

----------------------------------------   

[목차로](https://github.com/isp829/3dunitymulty/blob/master/README.md)  
[다음](https://github.com/isp829/3dunitymulty/blob/master/lecture/lecture3-6.md)  
-----------------------------
