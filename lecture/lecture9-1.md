다듬기
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-1.PNG" width="50%">  

* 지금은 게임이 시작되어도 외부에서 사람이들어 올 수 있다.  
* Launcher스크립트에 한줄 추가해서 게임이 시작되면 방이 안보이도록 해주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-2.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-3.PNG" width="50%">  

* 테스트 해보면 게임이 시작되면 방목록에서 사라진다. 

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-4.PNG" width="50%">  

* 지금은 게임오버되면 그냥 고정된 카메라 시점으로 보인다.  
* 게임오버되면 자유롭게 게임을 관전하게 수정해주자.  
* Game Object를 만들고 watcher라 이름붙여주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-5.PNG" width="50%">  

* Rigidbody를 넣어준다.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-6.PNG" width="50%">  

* Watcher안에 CameraHolder를 넣어주고 그안에 카메라, 그리고 그안에 TMP를 넣어주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-7.PNG" width="50%">  

* 글자 크기와 색깔 위치를 조절해주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-8.png" width="50%">  

* Canvas도 설정을 해주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-9.PNG" width="50%">  

* DieCamera 스크립트를 작성해주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-10.PNG" width="100%">  

* 플레이어 움직이는 스크립트랑 똑같은 구조다.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-11.png" width="100%">  

* BuildSettings=>ProjectSettings=>InputManager에서 Jump의 음수버튼을 x로 설정해주자.  
* 이렇게 되면 관전 카메라는 스페이스바를 누르면 위로 올라가고 x를 누르면 아래로 내려가면서 떠다닐꺼다.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-12.PNG" width="50%">  

* Watcher에 DieCamera스크립트를 넣어주고 설정을 해주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-13.PNG" width="50%">  

* 실행해보면 잘된다.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture9/lecture9-1/9-1-14.PNG" width="50%">  

* 맵에 여러 지형지물도 더 넣고 사람들도 불러서 게임을 즐겨보자

---------------------------   
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
        PhotonNetwork.CurrentRoom.IsVisible = false;//게임시작하면 안보임
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

* launcher 스크립트의 전문이다.  

---------------------------  
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class DieCamera : MonoBehaviour
{
    float verticalLookRotation;
    [SerializeField] float mouseSensitivity, sprintSpeed, walkSpeed, jumpForce, smoothTime;
    [SerializeField] GameObject cameraHolder;
    Rigidbody rb;
    Vector3 moveAmout;
    Vector3 smoothMoveVelocity;
    void Awake()
    {
        rb = GetComponent<Rigidbody>();
    }
    void Update()
    {
        Move();
        Look();
    }

    void Move()
    {
        Vector3 moveDir = new Vector3(Input.GetAxisRaw("Horizontal"), Input.GetAxisRaw("Jump"), Input.GetAxisRaw("Vertical")).normalized;
        moveAmout = Vector3.SmoothDamp(moveAmout, moveDir * sprintSpeed, ref smoothMoveVelocity, smoothTime);//if문을 한줄로 표현한것임
    }
    void Look()
    {
        transform.Rotate(Vector3.up * Input.GetAxisRaw("Mouse X") * mouseSensitivity);

        verticalLookRotation += Input.GetAxis("Mouse Y") * mouseSensitivity;
        verticalLookRotation = Mathf.Clamp(verticalLookRotation, -90f, 90f);

        cameraHolder.transform.localEulerAngles = Vector3.left * verticalLookRotation;
    }
    void FixedUpdate()
    {
        rb.MovePosition(rb.position + transform.TransformDirection(moveAmout) * Time.fixedDeltaTime);
    }

}
```

* Die Camera스크립트의 전문이다.  

---------------------------------   

[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
-----------------------------   
