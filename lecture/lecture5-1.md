기본조작 만들기    
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-1.PNG" width="50%">  

* CameraHolder를 만들어주고 그안에 카메라를 넣어준다.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-2.png" width="50%">    

* PlayerController에 rigidbody를 넣어주고 xyz축 회전을 막아주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-3.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-4.PNG" width="50%">  

* PlayerController스크립트를 만들어주자.  
* 일단은 마우스를 좌우로 이동시킨만큼 시야가 변하도록 해주자.  

---------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    [SerializeField] float mouseSensitivity, sprintSpeed, walkSpeed, jumpForce, smoothTime;
    //마우스감도 뛰는속도 걷는속도 점프힘 뛰기걷기바꿀때 가속시간
    float verticalLookRotation;
    bool grounded;//점프를 위한 바닥체크
    Vector3 smoothMoveVelocity;
    Vector3 moveAmount;//실제 이동거리

    Rigidbody rb;

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        transform.Rotate(Vector3.up*Input.GetAxis("Mouse X")*mouseSensitivity);
        //마우스 움직이는 정도*민감도만큼 각도 움직이기
    }
}

```

* PlayerController의 전문이다.  

------------------------------------------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-5.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-6.PNG" width="50%">  

* 실행하기전에 Game Scene에 바닥으로쓸 plane을 하나 추가해주고 위치를 조정해준다.  
* PlayerController에서 mouse sensitivity를 설정해주고 실행해보면 마우스 움직이는 만큼 내 캐릭터의 시야가 바뀐다.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-7.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-8.PNG" width="50%">  

* 코드를 더 추가해주자.  
* 마우스를 위 아래로 움직인만큼 카메라의 각도가 변하도록 해주자.  

---------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    [SerializeField] float mouseSensitivity, sprintSpeed, walkSpeed, jumpForce, smoothTime;
    [SerializeField] GameObject cameraHolder;
    //마우스감도 뛰는속도 걷는속도 점프힘 뛰기걷기바꿀때 가속시간
    float verticalLookRotation;
    
    Rigidbody rb;

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        transform.Rotate(Vector3.up*Input.GetAxis("Mouse X")*mouseSensitivity);
        //마우스 움직이는 정도*민감도만큼 각도 움직이기
        verticalLookRotation += Input.GetAxis("Mouse Y")*mouseSensitivity;
        //마우스 움직이는 정도*민감도만큼 각도 값 받기
        verticalLookRotation = Mathf.Clamp(verticalLookRotation, -90f, 90f);
        //y축 -90도에서 90도만 값으로 받음
        cameraHolder.transform.localEulerAngles = Vector3.left * verticalLookRotation;
        //받은 각도로 카메라도 돌려줌
    }
}

```
* 수정한 PlayerController스크립트의 전문이다.  

--------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-9.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-10.PNG" width="50%">  

* PlayerController프리펩에 cameraHolder를 추가해주고 실행해주자.  
* 이제는 카메라시점이 마우스 움직이는데로 상하 좌우 다 바뀐다.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-11.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-22.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-14.PNG" width="50%">  


* 바닥체크용으로 PlayerController에 큐브를 추가해준다.  
* 크기와 위치를 조절해주고 메쉬필터와 메쉬렌더러는 삭제해주자.  
* 큐브에 rigidbody를 추가해주고 istrigger를 체크해주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-13.PNG" width="50%">  


* PlayerGroundCheck스크립트를 작성해주자.  

---------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-15.PNG" width="50%">  


* 물체에 접촉 여부에 따라 다른값을 PlayerController에 보내도록 작성해주자.  
* 작성한 PlayerGroundCheck스크립트는 아까만든 큐브에 넣어주도록하자.  

---------------------------   
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
}

```
* PlayerGroundCheck스크립트의 전문이다.  

-------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-16.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-17.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-18.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-19.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-20.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture5/lecture5-1/5-1-21.PNG" width="50%">  

* PlayerController스크립트에 코드를 추가해주자.  
* 방향키로 움직이고 스페이스바를 누르면 점프하도록 코드를 짜주자.  

---------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

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

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
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
* 수정한 PlayerController스크립트의 전문이다.  

-----------------
[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture5-2.md)  
-----------------------------
