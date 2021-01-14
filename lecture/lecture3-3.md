다른 물체들과 상호작용 만들기
=======================
![collision](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-1.PNG)
![trigger](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-2.PNG)  
* unity에서 물체끼리 상호작용할때 제일 많이 쓰이는 OnCollision과 OnTrigger에 대해서 알아보자.  
---------------------------------------------------  
![collision](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-1.PNG)
* OnCollision은 상호작용할 두물체에 각각 rigidbody와 collider가 필요하다.
* 물체의 면끼리 접촉하는걸로 작동한다.  
* 상태에 따라 명령어를 Enter, Stay, Exit으로 표현할 수 있다.  
--------------------------------------------------------------------------------------    
![trigger](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-2.PNG)   
* OnTrigger는 상호작용한 두물체에 collider가 필요하지만, rigidbody는 하나만 있어도 된다.  
* 면끼리 접촉뿐만 아니라 서로 겹치는것도 된다.  
* 상태에 따라 명령어를 Enter, Stay, Exit으로 표현할 수 있다.  
------------------------------------------------------------- 
![stay](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-3.PNG)
![enter](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-4.PNG)
![exit](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-5.PNG) 
* 그림과 같이 stay는 접촉하고있을때, enter는 처음 접촉할때. exit는 접촉이 떨어질때 호출된다.  
-------------------------------------------------------------    
![최적화짤](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-6.png) 
* OnCollison이 물체마다 rigidbody를 사용하므로 게임내 object들이 많아질수록 OnTrigger에 비해 최적화가 안좋다.   
-------------------------------------------------------------    
![3-3-7](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-7.png)   
* 게임에 쓸 object들을 가져와준다.  
* 밟으면 죽는 장애물과 스테이지 클리어용 포탈을 가져왔다.  
------------------  
![3-3-8](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-8.png)   
* 각각 object에 polygon collider 2D를 넣어준다.  
* OnTrigger를 쓸것이므로 Is Trigger를 체크해 주자.    
-------------------------------------------------------------   
![3-3-9](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-9.png)   
* 장애물에는 spike tag를, 포탈에는 goal tag를 해준다.  
* 계속 쓸 object들이므로 prefab해주는게 좋다.  
-------------------------------------------------------------
![3-3-10](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-10.PNG)   
* 코드를 추가해 주자. 특정 object와 접촉하면 특정 scene을 불러와야 하므로 SceneManagement를 추가해주자.  
-------------------------------------------------------------   
![3-3-11](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-11.PNG)   
* 일단은 spike에 닿아도 재시작, goal에 닿아도 재시작하게 코드를 짜주자.   
-------------------------------------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.Mathematics;
using UnityEngine.SceneManagement;
public class test : MonoBehaviour
{
    public float moveSpeed = 5.0f; //플레이어 이동 속도
    public float jumpPower = 5.0f; //플레이어 점프 힘
    public Rigidbody2D rigid;
    float horizontal; //왼쪽, 오른쪽 방향값을 받는 변수
    public bool isground;//값을 받을 bool값
    public Transform groundCheck;//player발위치
    public float groundRadius = 0.2f;//측정할 범위
    public LayerMask whatIsGround;//어떤 layer를 측정할지
    public Animator animator;//애니메이터 추가
    private void Start()
    {
        rigid = GetComponent<Rigidbody2D>();
    }
    private void FixedUpdate()
    {
        isground = Physics2D.OverlapCircle(groundCheck.position, groundRadius, whatIsGround);
        if (isground == true)
        {
            animator.SetBool("jump", false);
        }
        Move(); //플레이어 이동
        Jump(); //점프   
    }
    void OnTriggerEnter2D(Collider2D col)
    {
        if (col.gameObject.tag == "spike")
        {
            SceneManager.LoadScene("test");
        }
        if (col.gameObject.tag == "goal")
        {
            SceneManager.LoadScene("test");
        }
    }
    void Jump()
    {
        if (Input.GetButton("Jump")) //점프 키가 눌렸을 때//ground이면서 스페이스바 누르면 
        {
            if (isground == true) //점프 중이지 않을 때
            {
                animator.SetBool("jump", true);//점프하면 애니메이터 설정 jump true.
                rigid.AddForce(Vector2.up * jumpPower, ForceMode2D.Impulse); //위쪽으로 힘을 준다.
                isground = false;
            }
            else return; //점프 중일 때는 실행하지 않고 바로 return.
        }
    }
    void Move()
    {
        horizontal = Input.GetAxis("Horizontal");
        if (horizontal != 0)
        {
            animator.SetBool("walk", true);//애니메이터 설정 walk를 true.
            if (horizontal >= 0) this.transform.eulerAngles = new Vector3(0, 0, 0);
            else this.transform.eulerAngles = new Vector3(0, 180, 0);
        }
        else 
        {
            animator.SetBool("walk", false);//좌우로 안움직이면 애니메이터 설정 walk false.
        }
        Vector3 dir = math.abs(horizontal) * Vector3.right; //변수의 자료형을 맞추기 위해 생성한 새로운 Vector3 변수
        this.transform.Translate(dir * moveSpeed * Time.deltaTime); //오브젝트 이동 함수
    }
}
```
* 코드 전문이다. 추가된 코드들이 무엇을 뜻하는지 생각하면서 써보자.  
-------------------------------------------------------------   
![3-3-12](https://github.com/isp829/HU/blob/master/images/lecture3/3-3/3-3-12.PNG)   
* 실행해보면 잘된다.  
-------------------------------------------------------------   
[목차로](https://github.com/isp829/HU/blob/master/README.md)  
[다음](https://github.com/isp829/HU/blob/master/lecture/lecture5-1.md)  
-----------------------------
    
