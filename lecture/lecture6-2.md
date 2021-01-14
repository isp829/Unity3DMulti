enemy 개선하기
=======================
![6-2-1](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-1.PNG)  
* enemy를 개선해보자.  
* 지금 enemy는 날라다니던 상태에서 조정을해서 걸어다녀서 장애물이 있어도 넘어가지 못한다.
* player에게 썼던 코드들을 살짝 수정해서 enemy도 장애물들을 넘어다니게 만들어주자.  
---------------------------------------------------    
![6-2-2](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-2.PNG)  
* player에게 넣어 줬던 것처럼 enemy에게도 foot을 넣어주고  
enemy는 우리가 보고 조작하는게 아니므로 eye도 넣어주자.  
---------------------------------------------------   
![6-2-3](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-3.PNG)  
* 땅바닥 체크말고도 눈앞에 뭐있는지 체크할 요소들을 추가해준다.  
---------------------------------------------------    
![6-2-4](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-4.PNG)  
* 점프 코드를 짜준다. 발이 바닥에 닿아있고, 눈앞에 벽이있으면 점프한다.  
---------------------------------------------------    
![6-2-5](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-5.PNG)  
* 실행해보면 눈앞에 벽이 있으면 바로 점프해서 뛰어넘고 진행한다.  
---------------------------------------------------    
![6-2-6](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-6.PNG)  
* 지금 enemy의 target이 player랑 waypoint일때의 차이점이 없어서 enemy가 그냥 걸어가는건지 아니면 날 잡으러 오는지 알 방법이 없다.  
* target이 player일때 바로 알 수 있는 방법을 생각해보자.  
---------------------------------------------------    
![6-2-7](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-7.PNG)  
* enemy의 target이 player이면 머리위에 느낌표가 뜨도록 만들어보자.  
---------------------------------------------------    
![6-2-8](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-8.PNG)  
* 3D object에서 textMeshPro를 만들어주자.  
* 원래는 유료 에셋이었는데 무료로 바뀌었다.  
---------------------------------------------------    
![6-2-9](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-9.PNG)  
* 유니티에서 사용하기 위해서는 TMPro헤더파일을 쓴다고 적어주어야 한다.  
---------------------------------------------------    
![6-2-10](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-10.PNG)  
![6-2-11](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-11.PNG)  
![6-2-12](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-12.PNG)   
* string을 받아 글자를 띄우는 함수를 만들고 enemy와 player의 상태에 따라 다른 글자들을 출력하게 하자.  
---------------------------------------------------    
![6-2-13](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-13.PNG)  
* 실행해 보니까 캐릭터가 뒤집힐때 글자도 같이 좌우 반전되서 보기 흉하다. 어떻게 해결 할 수 있을까?  
---------------------------------------------------    
![6-2-14](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-14.PNG)  
![6-2-15](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-15.PNG)  
* 빈gameObject를 하나 만들어주고 그안에 둘다 넣어주면 textMeshPro는 안뒤집힌다.  
* 추가로 코드를 넣어주어 textMeshPro의 위치가 enemy머리위에 오도록 해주자.  
---------------------------------------------------    
![6-2-16](https://github.com/isp829/HU/blob/master/images/lecture6/6-2/6-2-16.PNG)  
* 실행해 보면 아주 잘 작동한다.
---------------------------------------------------  
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.Mathematics;
using TMPro;

public class enemy : MonoBehaviour
{
    public float enemySpeed;
    public Transform target;
    public Transform[] waypoint;
    public int wayNumber;
    public Transform player;
    public float dist;
    public float checkDist;
    public float height;//높이측정
    //public float wayHeight;//방해물있을시 점프
    public Rigidbody2D rigid;//점프용 리지드바디
    public float jumpPower = 5.0f;
    public bool isground;//값을 받을 bool값
    public bool iswall;//장애물 체크
    public Transform groundCheck;//player발위치
    public Transform wallCheck;//장애물 인식
    public float groundRadius = 0.2f;//측정할 범위
    public LayerMask whatIsGround;//어떤 layer를 측정할지
    public Animator animator;//애니메이터 추가
    public TextMeshPro textMeshPro;
   
    void Update()
    {
        textMeshPro.transform.position = this.transform.position + Vector3.up * 6f;//글자가 머리위에 오게하기
        dist = Vector2.Distance(transform.position, player.position);
        if (dist < checkDist)//범위내에 player
        {
            height = math.abs((player.position.y) - (transform.position.y));
            if (height < 6)
            {
                target = player;
                say("!");
            }
            else
            {
                target = waypoint[wayNumber];
                say("?");
            }
        }
        else 
        {      
            target = waypoint[wayNumber];
            say("");
        }
        Vector2 onlyXTarget=new Vector2(target.position.x,transform.position.y);
        transform.position = Vector2.MoveTowards(transform.position, onlyXTarget, enemySpeed * Time.deltaTime);
        if (target.position.x <= transform.position.x)//얼굴뒤집기
        {
            this.transform.eulerAngles = new Vector3(0, 180, 0);
        }
        else
        {
            this.transform.eulerAngles = new Vector3(0, 0, 0); 
        }
        if ((target.position.x == transform.position.x)&&(target.position.x == waypoint[wayNumber].position.x))//y축 추가로 넣어도 되는데 넣을꺼면 바닥에 딱붙여야됨
        {  
            wayNumber++;
            wayNumber = wayNumber % 2;
            target = waypoint[wayNumber];
        }
        Jump();
    }

    private void FixedUpdate()
    {
        isground = Physics2D.OverlapCircle(groundCheck.position, groundRadius, whatIsGround);
        iswall = Physics2D.OverlapCircle(wallCheck.position, groundRadius, whatIsGround);
    }

    void Jump()
    {
        if (iswall == true) //벽감지시 점프
        {
            if (isground == true) //점프 중이지 않을 때
            {
                rigid.AddForce(Vector2.up*jumpPower, ForceMode2D.Impulse); //위쪽으로 힘을 준다.
                isground = false;
            }
            else return; //점프 중일 때는 실행하지 않고 바로 return.
        }
    }

    private void say(string text) 
    {
        textMeshPro.SetText(text);//string받아서 출력
    }   
}
    

```
* 수정한 enemy코드 전문이다.
----------------------------
[목차로](https://github.com/isp829/HU/blob/master/README.md)  
[다음](https://github.com/isp829/HU/blob/master/lecture/lecture6-3.md)  
-----------------------------
    


    

    
