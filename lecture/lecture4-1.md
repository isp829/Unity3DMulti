특수 발판들 만들기  
=======================
![4-1-1](https://github.com/isp829/HU/blob/master/images/lecture4/4-1-1.jpg)  
* 맵도 만들었고 플레이어도 만들었고 적도 만들었으면 이제 발판들을 만들자.
* 지금까지 했던 게임들의 발판을 참고해도 좋고 자기만의 아이디어가 있으면 구현해 보자.    
* 밟고있으면 점프력이 벽하는 발판들과 이동속도가 변하는 발판들을 구상해보자.  
------------------------------------ 
![4-1-2](https://github.com/isp829/HU/blob/master/images/lecture4/4-1-2.PNG)
* 코드에서 바닥을 밟고 있으면 이니까 OnCollisionStay2D를 사용해서 조건들을 달아주자.  
---------------------------  
![4-1-3](https://github.com/isp829/HU/blob/master/images/lecture4/4-1-3.PNG)  
* 이런 종류의 게임에서 항상 나오는 한번 밟으면 사라지는 바닥도  
Destroy(Object,time)을 사용하여 몇초후에 사라지게 만들어보자.  
---------------------------  
![4-1-4](https://github.com/isp829/HU/blob/master/images/lecture4/4-1-4.PNG)  
* 적당히 애니메이션도 적용하여 사라지는게 눈에 보이게 하자.  
-----------------------  
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
        if (Input.GetButton("Jump")) //점프 키가 눌렸을 때//ground이면서 스페이스바 누르면 
        {
            if (isground == true) //점프 중이지 않을 때
            {
                animator.SetBool("jump", true);//점프하면 애니메이터 설정 jump true.
                rigid.AddForce(Vector2.up * realJumpPower, ForceMode2D.Impulse); //위쪽으로 힘을 준다.
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
        this.transform.Translate(dir * realMoveSpeed * Time.deltaTime); //오브젝트 이동 함수
    }
}

```
------------------------------------------
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class platform : MonoBehaviour
{
    public Animator animator;//애니메이터 추가
    public float count=2.0f;
    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Player"))
        {
            Destroy(gameObject, count*Time.deltaTime);
            animator.SetBool("isPlayer", true);//바스라지는 애니메이션 설정.
        }
    }
}

```
* 각각 player에 들어가는 코드 수정본과 새로만든 부서지는 발판의 코드들이다.
--------------------------------
![4-1-5](https://github.com/isp829/HU/blob/master/images/lecture4/4-1-5.jpg)   
* 이 외에도 다양하게 만들어보자.(ex:중력 역전 발판)  
--------------------------------
[목차로](https://github.com/isp829/HU/blob/master/README.md)  
[다음](https://github.com/isp829/HU/blob/master/lecture/lecture6-1.md)  
-----------------------------

    
