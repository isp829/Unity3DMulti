기본조작 만들기   
=======================    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-1.PNG" width="50%">  

* PlayerController 설정을 해주고 실행해보자.  

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-2.PNG" width="50%">  

* 실행해보면 잘움직이고 시선도 마우스 따라서 움직인다.  

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-3.PNG" width="50%">  

* PlayerGroundCheck코드에 collider말고 collision도 추가해주자.  

---------------------------    
--------------------
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerGroundCheck : MonoBehaviour
{
    PlayerController playerController;//Player Controller 스크립트를 메서드로 사용하기 위해 선언
    void Awake()
    {
        playerController = GetComponentInParent<PlayerController>();      
    }

    void OnTriggerEnter(Collider other)
    {
        if (other.gameObject == playerController.gameObject)
            return;//해당 물체가 player면 무시
        playerController.SetGroundedState(true);
        //닿으면 true
    }

    void OnTriggerExit(Collider other)
    {
        if (other.gameObject == playerController.gameObject)
            return;//해당 물체가 player면 무시
        playerController.SetGroundedState(false);
        //떨어지면 true
    }

    void OnTriggerStay(Collider other)
    {
        if (other.gameObject == playerController.gameObject)
            return;//해당 물체가 player면 무시
        playerController.SetGroundedState(true);
        //닿고 있으면 true
    }

    void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject == playerController.gameObject)
            return;//해당 물체가 player면 무시
        playerController.SetGroundedState(true);
        //닿으면 true
    }

    void OnCollisionExit(Collision collision)
    {
        if (collision.gameObject == playerController.gameObject)
            return;//해당 물체가 player면 무시
        playerController.SetGroundedState(false);
        //떨어지면 true
    }

    void OnCollisionStay(Collision collision)
    {
        if (collision.gameObject == playerController.gameObject)
            return;//해당 물체가 player면 무시
        playerController.SetGroundedState(true);
        //닿고 있으면 true
    }
}
```
* 수정한 PlayrGroundCheck 스크립트의 전문이다.  

--------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-4.PNG" width="50%">  

* 빛 설정을 좀 바꿔주자.   
* window=>rendering=>lightning을 클릭해서 빛 설정 창을 열어주자.  

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-5.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-6.PNG" width="50%">  

* new lighting을 만들어주고 auto generate를 클릭해주자.  

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-7.PNG" width="50%">  

* 그리고 environment에서 skybox를 default skybox로 설정해주면 살짝 어둡던 game scene이 확 밝아진다.  

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-8.PNG" width="50%">  

* 빌드해서 두개를 같이 실행시켜보면 사진과 같이 두개의 플레이아거 같이 움직이고, 카메라위치도 같고 서로 화면에 움직임이 다르다. 
* 어떻게 해야될까?

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-9.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-10.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-11.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-12.PNG" width="50%">  

* 포톤뷰를 넣어주고 내 포톤 뷰가 아니면 작동안하게 코드를 짜주자.  
* 이렇게 하면 두개의 플레이어가 같이 움직이는건 해결 할 수 있을꺼다.  

---------------------------    
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;

public class PlayerController : MonoBehaviour
{
    [SerializeField] float mouseSensitivity, sprintSpeed, walkSpeed, jumpForce, smoothTime;
    [SerializeField] GameObject cameraHolder;
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

    public void SetGroundedState(bool _grounded)
    {
        grounded = _grounded;
    }

    void FixedUpdate()
    {
        rb.MovePosition(rb.position + transform.TransformDirection(moveAmount) * Time.fixedDeltaTime);
        //이동하는거는 계산 끝난 moveAmount만큼만 고정된시간(0.2초)마다에 맞춰서
    }
}

* 수정한 스크립트이다.  

```
--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-13.PNG" width="50%">  

* 실행해보면 캐릭터가 같이 움직이던 문제는 해결이 되었다.  
* 하지만 내 캐릭터에 붙여있어야 될 카메라가 다른 캐릭터에 붙어있고 여전히 서로 화면에 다르게 보인다.  

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-14.PNG" width="50%">  

* PlayerController스크립트를 더 수정해주자.  
* 내 포톤뷰가 아니면 카메라를 없애도록 코드를 추가해주자.  

---------------------------    
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;

public class PlayerController : MonoBehaviour
{
    [SerializeField] float mouseSensitivity, sprintSpeed, walkSpeed, jumpForce, smoothTime;
    [SerializeField] GameObject cameraHolder;
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
        if (!PV.IsMine)
        {
            Destroy(GetComponentInChildren<Camera>().gameObject);
            //내꺼 아니면 카메라 없애기
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

    public void SetGroundedState(bool _grounded)
    {
        grounded = _grounded;
    }

    void FixedUpdate()
    {
        rb.MovePosition(rb.position + transform.TransformDirection(moveAmount) * Time.fixedDeltaTime);
        //이동하는거는 계산 끝난 moveAmount만큼만 고정된시간(0.2초)마다에 맞춰서
    }
}

```

* 수정한 코드전문이다.     

----------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-15.PNG" width="50%">  

* 실행해보면 카메라가 내가 움직이는 캐릭터에 붙어있다.  
* 이제 서로 화면에 다르게 보이는 문제만 고치면된다.    

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-16.PNG" width="50%">  

* PlayerController 프리펩에서 PhotonView transform을 추가해준다.  
* 위치, 각도, 크기를 동기화 시키도록 체크해주자.  

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-17.PNG" width="50%">  

* 실행해보면 서로 잘보이고 화면도 똑같이 보인다.  
* 하지만 점프할때마다 엄청난 지연이 생기면서 부자연스럽게 움직인다. 
* 이건 어떻게 해결해야 될까?

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-18.PNG" width="50%">  

* rigidbody가 중복되서 그런거니까 PlayerController 스크립트에서 내 포톤뷰가아니면 rb를 삭제하도록 수정하면된다. 

---------------------------    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-2/5-2-19.PNG" width="50%">  

* fixed update에도 내 포톤뷰가 아니면 작동하지 않도록 수정해주자.  

---------------------------    

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;

public class PlayerController : MonoBehaviour
{
    [SerializeField] float mouseSensitivity, sprintSpeed, walkSpeed, jumpForce, smoothTime;
    [SerializeField] GameObject cameraHolder;
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
        if (!PV.IsMine)
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
------------------
[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture6-1.md)  
-----------------------------     
