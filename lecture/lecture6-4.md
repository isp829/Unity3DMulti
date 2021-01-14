아이템 만들기       
=======================      
![6-4-1](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-1.PNG)  
* 지금까지 만들어왔던 코드들을 살짝씩 수정하여 아이템들을 만들어보자.  
* 체력 증가 아이템과 무적 아이템을 만들어보자.  
------------------------------------    
![6-4-2](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-2.jpg) 
![6-4-3](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-3.jpg)  
* 체력 증가 아이템을 먹으면 그냥 단순히 최대체력이 늘어나고 아이템은 사라지게 만들자.  
* 무적 아이템을 먹으면 무적인걸 알기쉽게 색깔이 바뀌고 이동속도가 증가하고 적과 상호작용이 안일어나게 하자.  
먹은 아이템은 사라지게 하자.   
------------------------------------    
![6-4-4](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-4.PNG)
![6-4-5](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-5.PNG)
![6-4-6](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-6.PNG)  
* 체력증가 아이템을 위한 코드들이다 잘 재활용해서 만들어보자.  
------------------------------------    
![6-4-7](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-7.PNG)
![6-4-8](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-8.PNG)      
* 사용해 보니 아이템 먹을때마다 체력 회복 되고 아이템은 사라지는걸 알 수 있다. 
* 만약 아이템을 먹었는데도 아이템들이 삭제가 안된다면, OnCollision을 쓴게 아닐까 확인해보자. 
이경우에는 아이템에는 rigidbody가 없으므로 OnTrigger를 써야한다.     
------------------------------------    
![6-4-9](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-9.PNG) 
![6-4-6](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-6.PNG)  
* 무적 아이템을 위한 코드들이다 잘 재활용해서 만들어보자.   
------------------------------------    
![6-4-10](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-10.PNG)
![6-4-11](https://github.com/isp829/HU/blob/master/images/lecture6/6-4/6-4-11.PNG)  
* 사용해 보니 잘 작동한다.     
------------------------------------ 
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
        if (col.gameObject.tag == "healthItem") 
        {
            health++;
        }
        if (col.gameObject.tag == "invincibleItem")
        {
            StartCoroutine("GetInvincible");
        }
    }
    IEnumerator GetInvulnerable()//체력깎였을때 무적
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
    IEnumerator GetInvincible()//무적아이템 무적
    {
        moveSpeed = 4f * moveSpeed;
        jumpPower = 2f * jumpPower;
        Physics2D.IgnoreLayerCollision(9, 10, true);
        rend.material.color = Color.green;
        yield return new WaitForSeconds(10f);
        moveSpeed = 0.25f * moveSpeed;
        jumpPower = 0.5f * jumpPower;
        Physics2D.IgnoreLayerCollision(9, 10, false);
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
        }
        if (health <= 0)
        {
            SceneManager.LoadScene("test");//체력 0이면 scene다시불러오기 
        }
    }
}

```
* player코드 바뀐거 전문이다.  
-------------------------
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class itemGone : MonoBehaviour
{
    void OnTriggerEnter2D(Collider2D col)//아이템 먹으면 사라지게
    {
        if (col.gameObject.CompareTag("Player"))
        {
            Destroy(gameObject);
        }
    }
}
```
* 아까 새로 만든 먹은 아이템 사라지는 코드.
-------------
[목차로](https://github.com/isp829/HU/blob/master/README.md)  
[다음](https://github.com/isp829/HU/blob/master/lecture/lecture6-5.md)  
-----------------------------
