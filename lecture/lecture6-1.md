player개선하기  
=======================
![6-1-1](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-1.PNG)
* 지금 상태에서는 스페이스바를 짧게 누르건 길게 누르건 똑같은 높이를 점프를한다.  
* 점프키를 누른 시간에 비례해 점프하게는 못만들까?
--------------------------------------------------------
![6-1-2](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-2.PNG)
![6-1-3](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-3.PNG)
* 지금 쓰고있는 rigid.AddForc말고 rigid.velocity를 사용하여 스페이스바를 누르면 위쪽으로 힘을 밭고,  
스페이스바를 띈 순간 속도가 절반이 되도록 코드를 짜보자.  
--------------------------------------------------------
![6-1-4](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-4.PNG)
* 실행해보면 점프를 살짝 누르면 아주 낮게 점프를 하는걸 볼 수 있다.
--------------------------------------------------------
![6-1-5](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-5.PNG)
* 다음은 점프 판정을 좀 넉넉하게 만들어주자.  
* 게임할때도 난 분명히 스킬을 눌렀는데 스킬이 안나가서 죽는경우가 있었을 것이다.
* 바닥에서 떨어진지 0.2초 까지는 공중에서 점프 할 수 있게 해주자.   
--------------------------------------------------------
![6-1-6](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-6.PNG)  
![6-1-7](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-7.PNG)
* jumpTime과 jumpCounter를 만들어서 점프 판정을 좀 넉넉하게 만들어주자.   
--------------------------------------------------------
![6-1-8](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-8.PNG)
* 실행해 보면 바닥에서 떨어지고 나서 빠르게 점프하면 점프가 먹힌다.  
-----------------------   
![6-1-9](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-9.PNG)
* 점프를 늦게 눌러도 작동하게 만들었으니까 점프를 좀 일찍 눌러놔서 선입력을 받게 해주자.  
* 선입력이란 게임에서 내가 스킬을 시전하고 있는 와중에 다른기술 키를 누르면  
지금 쓰고 있는 기술이 끝나자마자 다른기술이 나가는 시스템이다.
* 롤에서도 CC기 맞기 전에 스킬을 미리 선입력 해놓으면 내 캐릭터는 CC기에 맞은 상태지만 스킬이 나간다.
-----------------------   
![6-1-10](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-10.PNG)  
![6-1-11](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-11.PNG)  
![6-1-12](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-12.PNG)  
* jumpBufferCount와 jumpBufferLength를 사용하여 코드를 수정해주자.  
* jumpBufferLentght의 시간만큼 선입력이 가능하다.  
* 이제 버튼 누른다고 점프 조건이 아니므로 코드들을 수정해주자.  
-----------------------   
![6-1-13](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-13.PNG)
* 실행해 보면 바닥에 닿기전에 점프키를 누르면 바닥에 닿자마자 점프한다.  
-----------------------   
![6-1-14](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-14.png)
* 보통 게임에서는 피격시 캐릭터가 불투명하게 변하면서 그동안은 무적 판정이 있다.
-----------------------   
![6-1-15](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-15.PNG)  
![6-1-16](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-16.PNG)  
![6-1-17](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-17.PNG)  
* Color기능과 Physics2D.IgnoreLayerCollision기능을 사용하여 구현해보자.
* Color의 a값을 조절하면 푸명토를 조절할 수 있다.  
* Physics2D.IgnoreLayerCollision기능을 사용하면 두 레이어 사이의 상호작용을 없앨 수 있다.  
* player를 layer9번에, enemy를 layer10번에 넣어주자.  
-----------------------   
![6-1-18](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-18.PNG)  
![6-1-19](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-19.PNG)  
![6-1-20](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-20.PNG)  
![6-1-21](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-21.PNG)  
* 코드들을 사용해 구현해주자.  
----------------------- 
![6-1-22](https://github.com/isp829/HU/blob/master/images/lecture6/6-1/6-1-22.PNG)  
* 실행해보면 enemy와 접촉시 반투명이 되고 3초동안 상호작용이 안일어난다.    
--------------  
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.Mathematics;
using UnityEngine.SceneManagement;
public class test : MonoBehaviour
{
    public float moveSpeed = 5.0f; //플레이어 이동 속도
    private float realMoveSpeed = 5.0f;//실제 이동 속도
    public float jumpPower = 5.0f; //플레이어 점프 힘
    private float realJumpPower = 5.0f;//실제 점프 힘
    public Rigidbody2D rigid;
    float horizontal; //왼쪽, 오른쪽 방향값을 받는 변수
    public bool isground;//값을 받을 bool값
    public Transform groundCheck;//player발위치
    public float groundRadius = 0.2f;//측정할 범위
    public LayerMask whatIsGround;//어떤 layer를 측정할지
    public Animator animator;//애니메이터 추가
    public float jumpTime = 0.2f;//점프 후입력
    public float jumpCounter;
    public float jumpBufferLength=0.2f;//점프 선입력
    public float jumpBufferCount;
    Renderer rend;
    Color c;
    private void Start()
    {
        rigid = GetComponent<Rigidbody2D>();
        rend = GetComponent<Renderer>();
        c = rend.material.color;
    }
    private void Update()
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
    IEnumerator GetInvulnerable() 
    {
        Debug.Log("!!!");
        Physics2D.IgnoreLayerCollision(9, 10,true);
        c.a = 0.5f;
        rend.material.color = c;
        yield return new WaitForSeconds(3f);
        Physics2D.IgnoreLayerCollision(9, 10, false);
        c.a = 1f;
        rend.material.color = c;
    }
    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("enemy"))
        {
            StartCoroutine("GetInvulnerable");
        }
    }
    private void OnCollisionStay2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("jumpGround"))
        {
            realJumpPower = 2 * jumpPower;
        }
        else if(collision.gameObject.CompareTag("stickGround"))
        {
            realJumpPower = jumpPower/2;
        }
        else
        {
            realJumpPower = jumpPower;
        }
        if (collision.gameObject.CompareTag("fastGround"))
        {
            realMoveSpeed = 2 * moveSpeed;
        }
        else if (collision.gameObject.CompareTag("slowGround"))
        {
            realMoveSpeed = moveSpeed / 2;
        }
        else
        {
            realMoveSpeed = moveSpeed;
        }

    }
    void Jump()
    {
        if (isground)
        {
            jumpCounter = jumpTime;
        }
        else
        {
            jumpCounter -= Time.deltaTime;
        }
        if (Input.GetButtonDown("Jump")) //점프 키가 눌렸을 때//ground이면서 스페이스바 누르면 
        {
            jumpBufferCount = jumpBufferLength; 
            //else return; //점프 중일 때는 실행하지 않고 바로 return.
        }
        else
        {
            jumpBufferCount -= Time.deltaTime;
        }
        if (Input.GetButtonUp("Jump")) 
        {
           rigid.velocity = new Vector2(rigid.velocity.x, rigid.velocity.y * 0.5f);//살짝 점프
           
        }
        if (Input.GetButton("Jump"))
        {
           animator.SetBool("jump", true);
        }
        if (jumpBufferCount>=0&&jumpCounter > 0) //점프 중이지 않을 때
        {
            //rigid.AddForce(Vector2.up * realJumpPower, ForceMode2D.Impulse); //위쪽으로 힘을 준다.//반점프 사용하기위해서 이거말고 velocity사용
            rigid.velocity = new Vector2(rigid.velocity.x, realJumpPower);
            jumpBufferCount = 0;//점프 바로 못하게
            isground = false;
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
        this.transform.Translate(dir * realMoveSpeed * Time.deltaTime); //오브젝트 이동 함수
    }
}
```
* 개선한 코드 전문이다.
----------------------------
[목차로](https://github.com/isp829/HU/blob/master/README.md)  
[다음](https://github.com/isp829/HU/blob/master/lecture/lecture6-2.md)  
-----------------------------
    
