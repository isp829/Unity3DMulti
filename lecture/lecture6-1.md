사용할 아이템 만들기      
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-1.PNG" width="50%">  

* 아이템을 만들기위해서 ItemInfo와 GunInfo 스크립트를 작성해주자.   

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-2.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-3.PNG" width="50%">  


* 각각 ItemInfo와 GunInfo의 스크립트이다.  
* ScriptableObject를 사용하여 간단하게 많은 양의 프리펩들에 정보를 넣어 줄 수 있다.  
* GunInfo는 ItemInfo를 받아서 New Gun menu를 만들도록 해주자.  

------------------------------------------------------  
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ItemInfo : ScriptableObject
//데이터를 저장하는 데 사용할 수 있는 데이터 컨테이너
//불러올때마다 사본이 생성되는 것을 방지하여 메모리 사용을 줄임
//프리팹이 있는 프로젝트의 경우 유용함 메모리에 데이터 사본을 하나만 저장
{
    public string itemName;
}

```

* ItemInfo 스크립트이다.

---------------
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
[CreateAssetMenu(menuName ="3Dgame/New Gun")]
//해당이름 가진 에셋메뉴 만들어주기
public class GunInfo : ItemInfo//아이템 인포로부터 받아옴
{
   
}

```

* GunInfo 스크립트이다.  

---------------

<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-4.PNG" width="50%">  


* Asset=>Items=>Guns폴더들을 만들어주자.  

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-5.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-6.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-7.PNG" width="50%">  


* 2개의 New Gun을 만들어준다.    
* 각각 Gun1과 Gun2로 이름 지어주고 item info에도 Gun1과 Gun2라고 써주자.  

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-8.PNG" width="50%">  

* PlayerController 프리펩에서 itemHolder를 만들어주고 Gun1Item과 Gun2Item을 넣어주자.  

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-9.PNG" width="50%">  

* Item 스크립트를 작정해주자.  

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-10.PNG" width="50%">  

* 간단하게 itemInfo를 받고 해당 아이템 game Object를 등록할 수 있게 해준다.  

---------------------------  
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Item : MonoBehaviour
{
    public ItemInfo itemInfo;
    public GameObject itemGameObject;
}

```

* Item스크립트이다.  

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-11.PNG" width="50%">  

* GunItem안에 Gun1과 Gun2의 빈 gameObject를 넣고 총 모델링으로 쓸 큐브들을 넣어주자.  

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-12.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-13.PNG" width="50%">  

* 두 총의 구분을 위해서 Gun1은 비교적 두껍게, Gun2은 얇게 만들어주자.  

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-14.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-15.PNG" width="50%">  

* Gun1/2Item에 item 스크립트를 넣어주고 각각의 ItemInfo와 ItemGameObject를 넣어주자.  

------------------------------------------------------     
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-16.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-17.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-18.PNG" width="50%">  

* PlayerContrller스크립트를 수정해주자.
* 아이템 목록을 만들어주고 현재 아이템이 뭔지 알 수 있게 int를 선언해준다.  
* 현재 아이템을 끼는 코드와 시작할때는 첫번째 아이템을 끼도록 코드를 수정해주자.  

------------------------------------------------------    
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;

public class PlayerController : MonoBehaviour
{
    [SerializeField] float mouseSensitivity, sprintSpeed, walkSpeed, jumpForce, smoothTime;
    [SerializeField] GameObject cameraHolder;
    [SerializeField] Item[] items;
    public int itemIndex;
    public int previousItemIndex=-1;//기본 아이템 값 없도록
    //마우스감도 뛰는속도 걷는속도 점프힘 뛰기걷기바꿀때 가속시간
    float verticalLookRotation;
    bool grounded;//점프를 위한 바닥체크
    Vector3 smoothMoveVelocity;
    Vector3 moveAmount;//실제 이동거리

    Rigidbody rb;
    PhotonView PV;

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
        PV = GetComponent<PhotonView>();
    }

    void Start()
    {
        if (PV.IsMine)
        {
            EquipItem(0);//시작하고 내 포톤뷰면 1번 아이템끼기(2번 아이템은 번호상 1이다)
        }
        else
        {
            Destroy(GetComponentInChildren<Camera>().gameObject);
            //내꺼 아니면 카메라 없애기
            Destroy(rb);
            //내거아니면 리지드 바디 없애주기
        }
    }

    void Update()
    {
        if (!PV.IsMine)
            return;//내꺼아니면 작동안함
        Look();
        Move();
        Jump();
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
        itemIndex = _index;
        items[itemIndex].itemGameObject.SetActive(true);//itemIndex번쨰 아이템 on
        if (previousItemIndex != -1)//만약 초기 상태가 아니라면
        {
            items[previousItemIndex].itemGameObject.SetActive(false);
            //내가 아까 꼈던 아이템은 off
        }
        previousItemIndex = itemIndex;//무한 사이클
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
}

```

* 수정한 PlayerController스크립트의 전문이다.  

-------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-19.png" width="50%">  

* 수정한 PlayerContrller에서 Gun1과 Gun2를 등록해주자.   

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-1/6-1-20.PNG" width="50%">  

* 실행해보면 1번 아이템으로 등록한걸 들고있는걸 볼 수 있다.  

------------------------------------------------------    
[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture6-2.md)  
-----------------------------
