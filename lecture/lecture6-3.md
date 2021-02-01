사용할 아이템 만들기      
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-1.PNG" width="50%">  

* item 스크립트를 수정해주자.  
* item자체를 추상 클래스로 선언해주고 use라는 추상 함수를 선언해주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-2.PNG" width="50%">  

* Gun 스크립트를 하나 만들어주자

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-3.PNG" width="50%">  

* item스크립트에서 사용했던거를 상속하도록 코드를 짜주자  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-4.PNG" width="50%">  

* 원거리 단발 아이템들을 관리하는 SingleShotGun스크립트를 만들어주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-5.PNG" width="50%">  

* 일단 간단하게 Gun으로부터 정보를 받고 호출되면 디버르 로그를 올리도록 코드를 작성해주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-6.PNG" width="50%">  

* Player Controller를 수정해서 마우스 왼쪽버튼을 누르면 Use를 호출하도록 해주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-7.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-8.png" width="50%">  

* Player Controller 프리펩에 있는 pistol과 rifle에 single shot gun스크립트를 넣어주자.  
* single shot gun스크립트에는 없는 내용이지만 gun스크립트에서 상속했으므로 gun스크립트 내용들이 뜬다.  
* 해당 정보들도 할당해주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-9.png" width="50%">  

* Player Controller에도 새로 아이템들을 등록해주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-10.PNG" width="50%">  

* 실행해보면 클릭할때마다 디버그 로그가 정상적으로 뜬다.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-11.PNG" width="50%">  

* 카메라에서 레이저를 발사하고 레이저가 물체에 닿으면 디버그 로그가 뜨도록 코드를 수정해주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-12.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-13.png" width="50%">  

* Rifle과 Pistol에있는 Gun스크립트에 PlayerController프리펩에 붙어있는 카메라를  할당해준다.    

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-14.PNG" width="50%">  

* 실행해보면 잘 작동한다.   

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-15.PNG" width="50%">  

* 입는 피해를 관리하는 IDamageable스크립트를 작성해주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-16.PNG" width="50%">  

* 인터페이스도 앞에 사용했던 추상 클래스와 마찬가지로 상속을 사용하기 위해 사용한다.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-17.PNG" width="50%">  

* 이제 Gun아이템들에 데미지를 넣어주자 
* Gun Info 스크립트를 수정해주자.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-18.PNG" width="50%">  

* Single Shot Gun스크립트도 수정해주자.  
* 레이캐스트에 맞은 대상이 피해를 받는 대상인지 확인하고 맞으면 아이템정보값에 맞는 피해를 받도록 수정해준다.  

------------------------------------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-19.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-3/6-3-20.png" width="50%">  

* Rifle과 Pistol의 damage값을 설정해주자.  

------------------------------------------------------    
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public abstract class Item : MonoBehaviour
{//추상 클래스로 선언
    public ItemInfo itemInfo;
    public GameObject itemGameObject;

    public abstract void Use();
    //추상 함수 선언
}

```

* Item스크립트의 전문이다

--------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public abstract class Gun : Item//아이템 스크립트에서 정보받아옴
{
    public abstract override void Use();
    //부모 클래스인 item에서 사용한 Use를 override해서 재정의
}

```

* Gun스크립트의 전문이다

--------------------------   
```
```

* GunInfo스크립트의 전문이다

--------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
[CreateAssetMenu(menuName ="3Dgame/New Gun")]
//해당이름 가진 에셋메뉴 만들어주기
public class GunInfo : ItemInfo//아이템 인포로부터 받아옴
{
    public float damage;
}

```

* SingleShotGun스크립트의 전문이다

--------------------------   
```
public interface IDamageable
//인터페이스는 추상 클래스와 같이 정보를 저장하는 용으로
{
    void TakeDamage(float damage);
}

```

* IDamageable스크립트의 전문이다

--------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;
using Hashtable = ExitGames.Client.Photon.Hashtable;//현재 게임 클라이언트가 쓰는 해쉬테이블 사용
using Photon.Realtime;

public class PlayerController : MonoBehaviourPunCallbacks//다른 포톤 반응 받아들이기
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
        for (int i = 0; i < items.Length; i++)
        {
            if (Input.GetKeyDown((i + 1).ToString()))//ToString으로 하면 입력받는 String을 숫자로 표현할 수 있다. 
            {
                EquipItem(i);
                //숫자키 1 2번으로 아이템 장착 가능
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

        if (Input.GetMouseButtonDown(0))//마우스 좌클릭시
        {
            items[itemIndex].Use();//들고있는 아이템 사용
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
}

```

* PlayerController스크립트의 전문이다

--------------------------   
[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture6-4.md)  
-----------------------------
