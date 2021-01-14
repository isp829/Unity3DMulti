UI만들기
=======================
![6-3-1](https://github.com/isp829/HU/blob/master/images/lecture6/6-3/6-3-1.PNG)  
* UI란 user interface의 줄임말로 사용자에게 게임 정보를 한눈에 보기 쉽게 제공하는 역할을 한다.  
* UI를 만들어 현재 player의 체력 상황을 알기쉽게 해주자.  
--------------------------------------     
![6-3-2](https://github.com/isp829/HU/blob/master/images/lecture6/6-3/6-3-2.PNG)  
![6-3-3](https://github.com/isp829/HU/blob/master/images/lecture6/6-3/6-3-3.png)  
* canvas를 만들어주고 설정을 해주자.  
--------------------------------------     
![6-3-4](https://github.com/isp829/HU/blob/master/images/lecture6/6-3/6-3-4.PNG)  
![6-3-5](https://github.com/isp829/HU/blob/master/images/lecture6/6-3/6-3-5.PNG)  
* 생명력 ui로 쓸 image들을 만들고 위치 조절을 해주자.   
--------------------------------------     
![6-3-6](https://github.com/isp829/HU/blob/master/images/lecture6/6-3/6-3-6.PNG)  
![6-3-7](https://github.com/isp829/HU/blob/master/images/lecture6/6-3/6-3-7.PNG)  
* 코드들을 사용해 생명력이 표시되도록 만들고 어떤 상황에 생명력이 1 줄어들지 생각해서 코드들을 수정하자.  
--------------------------------------  
![6-3-8](https://github.com/isp829/HU/blob/master/images/lecture6/6-3/6-3-8.PNG)  
* 실행해보면 enemy나 spike에 닿으면 체력이 줄어들고 무적상태가 된다.   
* 시작 체력을 3으로 하고 체력이 0이면 scene을 다시불러오도록 코드를 수정하자.  
--------------------------------------     
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.Mathematics;
using UnityEngine.SceneManagement;
using UnityEngine.UI;
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
    Renderer rend;//스프라이트 렌더러   
    Color c;//색깔 조정
    public int health;//현재 생명력
    public Image[] hearts;//생명력 이미지
    public Sprite healthSprite;//생명력 스프라이트 
    private void Start()
    {
        rigid = GetComponent<Rigidbody2D>();
        rend = GetComponent<Renderer>();
        c = rend.material.color;
        health = 3;
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
        healthCheck();//체력확인
    }
    void OnTriggerEnter2D(Collider2D col)
    {
        if (col.gameObject.tag == "spike")
        {
            StartCoroutine("GetInvulnerable");
           
        }
        if (col.gameObject.tag == "goal")
        {
            SceneManager.LoadScene("test");
        }
    }
    IEnumerator GetInvulnerable() 
    {
        health--;
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
        if (collision.gameObject.tag=="enemy")
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
    void healthCheck() 
    {
        for (int i = 0; i < hearts.Length; i++) 
        {
            hearts[i].sprite = healthSprite;
            if (i < health)
            {
                hearts[i].enabled = true;
            }
            else 
            {
                hearts[i].enabled = false;
            }
            if (health <= 0)
            {
                SceneManager.LoadScene("test");//체력 0이면 scene다시불러오기 
            }
        }
    }
}

```
* 수정한 코드들 전문이다.  
-------------
[목차로](https://github.com/isp829/HU/blob/master/README.md)  
[다음](https://github.com/isp829/HU/blob/master/lecture/lecture6-4.md)   
-----------------------------  

