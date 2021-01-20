메뉴 만들기
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-1.png" width="50%">   

* Find Room Menu에서 썼던 이미지를 Room Menu에 복사해준다. 

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-2.PNG" width="50%">  

* 복사한 이미지 이름을 바꿔주고 TMP를 넣어준다. 

----------------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-3.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-3.png" width="50%">  

* 텍스트창과 텍스트 크기조절, 위치조절을 해준다. 

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-5.png" width="50%">  

* 텍스트를 프리펩화 시켜준다. 

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-5/3-6-6.PNG" width="50%">  

* Player List Item 스크립트를 작성한다. 

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-7.PNG" width="50%">  

* 이름을 받아서 플레이어에게 이름을 주고 플레이어가 나가면 이름표를 지우도록 스크립트를 작성해주자. 

----------------------------------------   
```
using Photon.Pun;
using Photon.Realtime;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using TMPro;

public class PlayerListItem : MonoBehaviourPunCallbacks//다른 포톤 반응 받아들이기
{
    [SerializeField] TMP_Text text;
    Player player;//포톤 리얼타임은 Player를 선언 할 수 있게 해준다.

    public void SetUp(Player _player)
    {
        player = _player;
        text.text = _player.NickName;//플레이어 이름 받아서 그사람 이름이 목록에 뜨게 만들어준다. 
    }

    public override void OnPlayerLeftRoom(Player otherPlayer)//플레이어가 방떠났을때 호출
    {
        if (player == otherPlayer)//나간 플레이어가 나면?
        {
            Destroy(gameObject);//이름표 삭제
        }
    }

    public override void OnLeftRoom()//방 나가면 호출
    {
        Destroy(gameObject);//이름표 호출
    }
}

```

* Player List Item 스크립트의 전문이다.   

--------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-8.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-9.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-10.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-11.PNG" width="50%">  

* Launcher스크립트도 수정해준다.   
* 방에 내가 참여가하거나 다른 플레이어가 참여하면 이름을 붙여주고 이름표가 뜨도록 해준다. 

---------------------------------------- 
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;//포톤 기능 사용
using TMPro;//텍스트 메쉬 프로 기능 사용
using Photon.Realtime;
using System.Linq;

public class Launcher : MonoBehaviourPunCallbacks//다른 포톤 반응 받아들이기
{
    public static Launcher Instance;//Launcher스크립트를 메서드로 사용하기 위해 선언

    [SerializeField] TMP_InputField roomNameInputField;
    [SerializeField] TMP_Text errorText;
    [SerializeField] TMP_Text roomNameText;
    [SerializeField] Transform roomListContent;
    [SerializeField] GameObject roomListItemPrefab;
    [SerializeField] Transform playerListContent;
    [SerializeField] GameObject playerListItemPrefab;

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
        PhotonNetwork.NickName = "Player " + Random.Range(0, 1000).ToString("0000");
        //들어온사람 이름 랜덤으로 숫자붙여서 정해주기
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
        Player[] players = PhotonNetwork.PlayerList;
        for (int i = 0; i < players.Count(); i++)
        {
            Instantiate(playerListItemPrefab, playerListContent).GetComponent<PlayerListItem>().SetUp(players[i]);
            //내가 방에 들어가면 방에있는 사람 목록 만큼 이름표 뜨게 하기
        }
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

    public override void OnPlayerEnteredRoom(Player newPlayer)//다른 플레이어가 방에 들어오면 작동
    {
        Instantiate(playerListItemPrefab, playerListContent).GetComponent<PlayerListItem>().SetUp(newPlayer);
        //instantiate로 prefab을 playerListContent위치에 만들어주고 그 프리펩을 이름 받아서 표시. 
    }
}

```

* 수정한 Launcher스크립트의 전문이다.  

-----------------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-12.png" width="50%">  

* Player List Item의 프리펩의 크기를 조절해준다.  
* Player List Item 스크립트를 넣어주고 요소들을 할당해준다.  

----------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-13.png" width="50%">  

* canvas에 새로 launcher에 추가한 요소들을 넣어준다.  

----------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-6/3-6-14.PNG" width="50%">  

* 빌드한다음 여러개를 실행시켜보면 새로운 사람이 방에 들어올때마다 목록에 추가되는걸 볼 수 있다.  

----------------------- 
[목차로](https://github.com/isp829/3dunitymulty/blob/master/README.md)  
[다음](https://github.com/isp829/3dunitymulty/blob/master/lecture/lecture3-7.md)  
-----------------------------
