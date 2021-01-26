메뉴 만들기
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-1.PNG" width="50%">   

* start game버튼을 누르면 게임이 시작되도록 해주자.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-2.PNG" width="50%">   

* canvas에도 새로 추가한 요소들을 넣어주자. 

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-3.PNG" width="50%">   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-4.PNG" width="50%">   

* 실행해보면 방에 들어가서 방을 만든사람만 게임 실행 버튼을 누를 수 있다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-5.PNG" width="50%">   

* 하지만 지금 문제가 있는데 방을 만들고 떠나고 다시 만들기를 반복하면 아까 방만들때 있던 호스트의 이름이 계속 남는다.   
* 또 호스트가 방을 만든다음에 떠나도 다른사람 방목록에는 아직 방이 있는걸로 뜬다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-7.PNG" width="50%">   

* 일단 room list item스크립트를 열어서 roominfo를 퍼블릭으로 해주자.  

-------------------------------------------------------------   
```
using Photon.Realtime;
using System.Collections;
using System.Collections.Generic;
using TMPro;
using UnityEngine;

public class RoomListItem : MonoBehaviour
{
    [SerializeField] TMP_Text text;

    public RoomInfo info;//포톤 리얼타임의 방정보 기능. 퍼블릭으로 선언해서 다른곳에서 접근 가능하도록 수정. 
    public void SetUp(RoomInfo _info)//방정보 받아오기
    {
        info = _info;
        text.text= _info.Name;
    }

    public void OnClick()
    {
        Launcher.Instance.JoinRoom(info);//런처스크립트 메서드로 JoinRoom실행
    }
}

```

* 수정한 RoomListItem스크립티의 전문이다. 

----------------------- 
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-6.PNG" width="50%">   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-8.PNG" width="50%">   

* launcher스크립트도 수정해주자.  
* 방에 들어가면 전에있던 이름표들을 없애주는 코드를 만들어준다.  
* 또 사라진 방은 방목록에 안뜨게 수정해준다.  

-------------------------  
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
    [SerializeField] GameObject startGameButton;

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
        PhotonNetwork.AutomaticallySyncScene = true;//자동으로 모든 사람들의 scene을 통일 시켜준다. 
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
        foreach (Transform child in playerListContent)
        {
            Destroy(child.gameObject);//방에 들어가면 전에있던 이름표들 삭제
        }
        for (int i = 0; i < players.Count(); i++)
        {
            Instantiate(playerListItemPrefab, playerListContent).GetComponent<PlayerListItem>().SetUp(players[i]);
            //내가 방에 들어가면 방에있는 사람 목록 만큼 이름표 뜨게 하기
        }
        startGameButton.SetActive(PhotonNetwork.IsMasterClient);//방장만 게임시작 버튼 누르기 가능
    }

    public override void OnMasterClientSwitched(Player newMasterClient)//방장이 나가서 방장이 바뀌었을때
    {
        startGameButton.SetActive(PhotonNetwork.IsMasterClient);//방장만 게임시작 버튼 누르기 가능
    }

    public override void OnCreateRoomFailed(short returnCode, string message)//방 만들기 실패시 작동
    {
        errorText.text = "Room Creation Failed: " + message;
        MenuManager.Instance.OpenMenu("error");//에러 메뉴 열기
    }


    public void StartGame()
    {
        PhotonNetwork.LoadLevel(1);//1인 이유는 빌드에서 scene 번호가 1번씩이기 때문이다. 0은 초기 씬.
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
            if (roomList[i].RemovedFromList)//사라진 방은 취급 안한다. 
                continue;
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

* 수정한 launcher스크립트의 전문이다.  

-------------------------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-9.PNG" width="50%">   

* menu manager스크립도 수정해주자.  
* 함수 호출할때 똑같은 for문이 중복해서 돌아가므로 한번만 작동하게 해주자.  
* 이건 안고쳐도 메뉴 작동하는데는 문제가 없지만 바꿔주자.  

-------------------------------------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MenuManager : MonoBehaviour
{
    public static MenuManager Instance;//다른 class에서도 호출가능

    [SerializeField] Menu[] menus;//SerializedField를 사용하면 우리는 public처럼 쓸 수 있지만  public이 아니여서 외부에서는 못만짐.

    private void Awake()
    {
        Instance = this;
    }

    public void OpenMenu(string menuName)
    {
        for (int i = 0; i < menus.Length; i++)
        {
            if (menus[i].menuName == menuName)//string을 받아서 해당이름 가진 메뉴를 여는 스크립트
            {
                menus[i].Open();//오픈 메뉴(스트링)에 있는 for문이 오픈 메뉴(메뉴)에도 똑같이 있어서 중복을 피하고자 코드 수정.  
            }
            else if (menus[i].open)
            {
                CloseMenu(menus[i]);
            }
        }
    }

    public void OpenMenu(Menu menu)
    {
        for (int i = 0; i < menus.Length; i++)
        {
            if (menus[i].open)
            {
                CloseMenu(menus[i]);
            }
        }
        menu.Open();
    }

    public void CloseMenu(Menu menu)
    {
        menu.Close();
    }
}

```

* 수정한 MenuManager 스크립트의 전문이다.

----------------------------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-7/3-7-10.PNG" width="50%">   

* 실행해 보면 아까 문제들이 다 사라졌다.  

-------------------------  

[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture4-1.md)  
-----------------------------
