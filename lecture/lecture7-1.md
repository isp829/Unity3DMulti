술래 정하기     
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-1.PNG" width="50%">  

* 이제 '술래'잡기 게임이니까 술래를 정해주는 코드를 작성해주자.  
* GameController 스크립트를 만들어준다.

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-2.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-3.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-4.PNG" width="100%">  

* PlayerController로부터 리스트를 받아서 그중에 한사람을 술래로 정해주는 코드를 구현하자.  
* 게임이 시작되고 3초후에 술래가 정해지도록 지연을 넣자.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-5.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-6.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-7.png" width="100%">  

* PlayerController 스크립트도 수정해주자.  
* 체력과 술래인지 아닌지를 표현하는 텍스트를위해 TMP를 사용해주자. 
* 술래가된 캐릭터는 혼자 색깔이 바뀌므로 Renderer로 선언해주자.  
* GameController와의 정보 주고 받기를 위해서 PlayerController도 선언해주고 GameController를 사용한다고 선언해주고 추가해주자.   

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-8.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-9.png" width="100%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-10.PNG" width="50%">  

* 기존에는 시작하자마자 아이템을 꼈지만 이제 술래만 아이템을 사용할 수 있으므로 시작할때는 아무것도 안들게 해준다.   
* 술래한테 패널티를 주기위해 총을 쏠때마다 일정량의 체력이 깎이도록 해주자.  
* 술래면 화면에 모두잡으라 텍스트가 나오고 아니면 도망가라 텍스트가 나오게 해준다.  
* 마지막으로 모두에게 술래가 누군지 알려주기위해 술래정하기와 술래 색변경을 RPC로 해준다.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture7/lecture7-1/7-1-11.PNG" width="50%">  

* 실행해보면 게임시작 3초후에 술래가 정해지고 술래는 검정색이된다.  
* 술래에게 맞으면 GameOver가된다.  

----------------------------------------------  
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;
using System.IO;

public class GameController : MonoBehaviour
{
    public int whichPlayerIsBoss;
    public static GameController GC;
    public List<PlayerController> Players = new List<PlayerController>();
    void Awake()
    {
        GC = this;
    }
    IEnumerator ExampleCoroutine()//게임시작 3초후 술래가 정해지도록 지연을 둔다.  
    {
        Debug.Log("Started Coroutine at timestamp : " + Time.time);
        //초세기 시작
        yield return new WaitForSeconds(3);
        //3초세기
        Debug.Log("Finished Coroutine at timestamp : " + Time.time);
        //초세기 끝
        if (PhotonNetwork.IsMasterClient)
        {
            Debug.Log("Start Pick Boss");
            //술래정하기 시작
            PickBoss();
            //술래 정하기
        }
    }
    void Start()
    {
        StartCoroutine(ExampleCoroutine());
    }
    void PickBoss()
    {
        List<PlayerController> PlayerList = new List<PlayerController>(Players);
        //PlayerController달고있는 놈들 리스트화 시키기
        whichPlayerIsBoss = Random.Range(0, PlayerList.Count);
        //그중에 한놈 술래로 정해주기
        Debug.Log("We have " + PlayerList.Count);
        //현재있는 사람숫자 보고
        Debug.Log("Boss Number is " + whichPlayerIsBoss);
        //몇번이 술래인지 말해줌
        Players[whichPlayerIsBoss].GetComponent<PhotonView>().RPC("SetBoss", RpcTarget.All, true);
        //해당 사람이 술래가됨
        
    }
}
```

* Game Controller 스크립트의 전문이다. 

------------------------------------------
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;
using Hashtable = ExitGames.Client.Photon.Hashtable;//현재 게임 클라이언트가 쓰는 해쉬테이블 사용
using Photon.Realtime;
using static GameController;//게임 컨트롤러 사용
using TMPro;

public class PlayerController : MonoBehaviourPunCallbacks/*다른 포톤 반응 받아들이기*/,IDamageable//인터페이스 불러오기
{
    [SerializeField] float mouseSensitivity, sprintSpeed, walkSpeed, jumpForce, smoothTime;
    //마우스감도 뛰는속도 걷는속도 점프힘 뛰기걷기바꿀때 가속시간
    [SerializeField] GameObject cameraHolder;
    [SerializeField] Item[] items;
    public bool isBoss;
    public int itemIndex;
    public int previousItemIndex=-1;//기본 아이템 값 없도록
    
    float verticalLookRotation;
    bool grounded;//점프를 위한 바닥체크
    Vector3 smoothMoveVelocity;
    Vector3 moveAmount;//실제 이동거리

    Rigidbody rb;
    PhotonView PV;

    const float maxHealth = 100f;//최대체력
    float currentHealth = maxHealth;//시작체력

    PlayerManager playerManager;
    //플레이어 매니저 선언
    public static PlayerController playerController;
    Renderer capsuleColor;

    public TMP_Text healthText;
    public TMP_Text bossText;
    void Awake()
    {
        rb = GetComponent<Rigidbody>();
        PV = GetComponent<PhotonView>();
        isBoss = false;//초기 술래끄기
        playerManager = PhotonView.Find((int)PV.InstantiationData[0]).GetComponent<PlayerManager>();
        //플레이어 매니저 정의 PlayerManager에서 생성한 인스턴스를 찾게된다.  
        playerController = GetComponent<PlayerController>();
        //플레이어 컨트롤러를 가지도록 해주자
        GC.Players.Add(this);
        //게임컨트롤러 목록에 이놈들을 추가
        Debug.Log("Add complete");
        //추가완료
    }

    void Start()
    {
        if (!PV.IsMine)
        {
            Destroy(GetComponentInChildren<Camera>().gameObject);
            //내꺼 아니면 카메라 없애기
            Destroy(rb);
            //내거아니면 리지드 바디 없애주기
        }//술래만 무기를 가질수 있도록 해줘야하니까 맨처음에는 아무도 무기를 안낀다. 
        capsuleColor = gameObject.GetComponent<Renderer>();
    }

    void Update()
    {
        if (!PV.IsMine)
            return;//내꺼아니면 작동안함
        Look();
        Move();
        Jump();
        healthText.text = ("Current Health: " + currentHealth.ToString());
        if (isBoss == true)
        {
            bossText.text = ("Catch All");
            // 술래면 다잡아
            PV.RPC("RPC_SetColor", RpcTarget.AllBuffered);

            for (int i = 0; i < items.Length; i++)
            {
                if (Input.GetKeyDown((i + 1).ToString()))
                {
                    EquipItem(i);
                    break;
                }
            }

            if (Input.GetAxisRaw("Mouse ScrollWheel") > 0f)//마우스 스크롤 움직이면
            {
                if (itemIndex >= items.Length - 1)//만약 아이템 목록끝에 다다르면
                {
                    EquipItem(0);//맨처음 아이템으로
                }
                else
                {
                    EquipItem(itemIndex + 1);//아니면 다음 아이템으로
                }
            }
            else if (Input.GetAxisRaw("Mouse ScrollWheel") < 0f)//마우스 스크롤 반대로 움직이면
            {
                if (itemIndex <= 0)//아이템 목록 맨처음보다 뒤로가면?
                {
                    EquipItem(items.Length - 1);//맨 끝 아이템으로

                }
                else
                {
                    EquipItem(itemIndex - 1);//아니면 이전 아이템으로
                }
            }

            if (Input.GetMouseButtonDown(0) && (items[0].itemGameObject.activeSelf == true || items[1].itemGameObject.activeSelf == true))
            {
                if (items[0].itemGameObject.activeSelf == true)
                {
                    TakeDamage(10);
                    //술래는 사용하는 아이템을쓰면 체력이 깎임
                }
                if (items[1].itemGameObject.activeSelf == true)
                {
                    TakeDamage(5);
                    //술래는 사용하는 아이템을쓰면 체력이 깎임
                }
                items[itemIndex].Use();
                //마우스 좌클릭시 들고있는 아이템 사용
            }

        }
        else
        {
            bossText.text = ("Run Away");
            //술래아니면 도망가
        }
        if (transform.position.y < -10f)//맵밖으로 나감
        {
            Die();
        }
    }
   
    void Look() 
    {
        transform.Rotate(Vector3.up * Input.GetAxis("Mouse X") * mouseSensitivity);
        //마우스 움직이는 정도*민감도만큼 각도 움직이기
        verticalLookRotation += Input.GetAxis("Mouse Y") * mouseSensitivity;
        //마우스 움직이는 정도*민감도만큼 각도 값 받기
        verticalLookRotation = Mathf.Clamp(verticalLookRotation, -90f, 90f);
        //y축 -90도에서 90도만 값으로 받음
        cameraHolder.transform.localEulerAngles = Vector3.left * verticalLookRotation;
        //받은 각도로 카메라도 돌려줌
    }

    void Move()
    {
        Vector3 moveDir = new Vector3(Input.GetAxisRaw("Horizontal"), 0, Input.GetAxisRaw("Vertical")).normalized;
        //벡더방향을 가지지만 크기는 1로 노말라이즈
        moveAmount = Vector3.SmoothDamp(moveAmount, moveDir * (Input.GetKey(KeyCode.LeftShift) ? sprintSpeed : walkSpeed), ref smoothMoveVelocity, smoothTime);
        //왼쪽 쉬프트가 누르면 뛰는속도, 나머지는 걷는속도로하기
        //smoothTime만큼에 걸쳐서 이동해주기. 
    }

    void Jump()
    {
        if (Input.GetKeyDown(KeyCode.Space) && grounded)//땅위에서 스페이스바 누르면
        {
            rb.AddForce(transform.up * jumpForce);//점프력만큼위로 힘받음
        }
    }

    void EquipItem(int _index)
    {
        if (_index == previousItemIndex)
            return;//입력받은 숫자가 아까 받은 숫자랑 똑같으면 아무일도 안해준다.  
        itemIndex = _index;
        items[itemIndex].itemGameObject.SetActive(true);//itemIndex번쨰 아이템 on
        if (previousItemIndex != -1)//만약 초기 상태가 아니라면
        {
            items[previousItemIndex].itemGameObject.SetActive(false);
            //내가 아까 꼈던 아이템은 off
        }
        previousItemIndex = itemIndex;//무한 사이클

        if (PV.IsMine)
        {
            Hashtable hash = new Hashtable();
            hash.Add("itemIndex", itemIndex);
            //이제 hash[itemindex]가 호출되면 현재 아이템번호가 호출된다.    
            PhotonNetwork.LocalPlayer.SetCustomProperties(hash);
            //포톤으로 모든 사람에게 내가 현재끼고 있는 아이템 번호를 알려준다.  
        }
    }

    public override void OnPlayerPropertiesUpdate(Player targetPlayer, Hashtable changedProps)
    //다른 플레이어의 아이템 번호 받아들이기
    {
        if (!PV.IsMine && targetPlayer == PV.Owner)//내꺼가 아니라 다른사람꺼일때
        {
            EquipItem((int)changedProps["itemIndex"]);
            //끼고있는 아이템 정보 받아들이기
        }
    }

    public void SetGroundedState(bool _grounded)
    {
        grounded = _grounded;
    }

    void FixedUpdate()
    {
        if (!PV.IsMine)
            return;//내꺼아니면 작동안함
        rb.MovePosition(rb.position + transform.TransformDirection(moveAmount) * Time.fixedDeltaTime);
        //이동하는거는 계산 끝난 moveAmount만큼만 고정된시간(0.2초)마다에 맞춰서
    }

    public void TakeDamage(float damage)
    {
        //IDamageable 인터페이스에 있는 함수 재정의
        PV.RPC("RPC_TakeDamage", RpcTarget.All,damage);
        //피해를 입힌사람이 해당 이름가진 함수를 RpcTaget(지금은 모든 플레이어)에게 적용되도록 호출
        //Rpc를 통해 받은 피해를 모두에게 전달한다
    }

    [PunRPC]//Pun Remote Procedure Call의 약자로써 원격제어를 통해 함수를 실행시키는 기능이다.  
    void RPC_TakeDamage(float damage)
    {
        //모두에게 전달됨
        if (!PV.IsMine)
            return;//피해입은놈 아니면 실행안됨
        Debug.Log("took damage " + damage);
        currentHealth -= damage;
        if (currentHealth <= 0)
        {
            Die();
        }
    }

    void Die()
    {
        GC.Players.Remove(this);
        playerManager.Die();
    }
    [PunRPC]
    void SetBoss(bool _isBoss)
    {
        //술래를 정해주는 RPC
        isBoss = _isBoss;
        Debug.Log("Boss " + isBoss);
    }

    [PunRPC]
    void RPC_SetColor()
    {
        //술래색깔을 검정색으로 해준다.  
        capsuleColor.material.color = Color.black; ;
    }
}
```

* Player Controller 스크립트의 전문이다.  

---------------------   
[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture8-1.md)  
-----------------------------   
