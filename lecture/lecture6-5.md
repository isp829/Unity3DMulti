사용할 아이템 만들기      
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-1.PNG" width="100%">  

* PlayerManager 스크립트를 수정해주자.  
* SpawnManager로부터 Spawnpoint를 받아와서 인스턴스 생성할때 그 위치에 생성하도록 해주자.  

--------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-2.PNG" width="50%">  

* SpawnPoint 프리펩에 SpawnPoint 스크립트를 넣어주고 시작하면 사라지는 Graphics에 capsule을 넣어준다.   

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-3.PNG" width="50%">  

* SpwanManager에 SpawnManager스크립트를 넣어준다.  
* Spawnpoint는 아까 코드에서 Spawnpoint스크립트를 가진 모든 요소들이 자동으로 할당되므로 일일이 넣어줄 필요없다.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-4.PNG" width="50%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-5.PNG" width="50%">    

* 5개를 같이 실행해서 여러번 테스트해봤다.  
* 각자 5개 모두 랜덤하게 시작지점을 가지는걸 볼 수 있다.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-6.PNG" width="50%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-7.PNG" width="50%">  
 
* Rifle과 Pistol에 PhotonView를 달아준다.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-8.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-9.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-10.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-11.PNG" width="50%">  

* Single Shot Gun 스크립트를 수정해주자.  
* PhotonView를 추가해주고 RayCast에 맞으면 맞은 위치를 말하도록 해주자.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-12.PNG" width="50%">  

* 실행해보면 RayCast에 맞은 위치가 나온다.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-13.PNG" width="50%">  

* Gun 스크립트를 수정해주자.  
* 맞은 위치를 표시할 GameObject를 선언해주자.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-14.PNG" width="50%">  

* Game Scene에서 3D Object Quad를 만들어주고 크기를 줄여준다음 아까 만든 Spawnpoint 메테리얼을 적용시켜주자. 
* Quad는 한쪽면에서만 보이므로 180도 틀어준다.  
* Collider도 없애주자.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-15.PNG" width="50%">  

* 프리펩화 시켜준다.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-16.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-17.PNG" width="100%">  

* Single Shot Gun 스크립트를 수정해주자.  
* RayCast가 맞은위치에 BulletImpact를 생성하도록 코드를 추가해주자.  
* 자국이 남는 방향은 물체의 방향과 동일하게 하도록 hitNormal을 써주자.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-18.png" width="50%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-19.png" width="50%">  

* PlayerController 프리펩에 있는 Rifle과 Pistol에 BulletImpact를 넣어주자.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-5/6-5-20.PNG" width="50%">  

* 게임을 실행하면 이런식으로 자국이 남는다.  

----------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;
using System.IO;//path사용위해

public class PlayerManager : MonoBehaviour
{
    PhotonView PV;//포톤뷰 선언
    GameObject controller;//PlayerController로쓰이는 캡슐

    void Awake()
    {
        PV = GetComponent<PhotonView>();   
    }

    void Start()
    {
        if (PV.IsMine)//내 포톤 네트워크이면
        {
            CreateController();//플레이어 컨트롤러 붙여준다. 
        }
    }
    void CreateController()//플레이어 컨트롤러 만들기
    {
        Transform spawnpoint = SpawnManager.Instance.GetSpawnpoint();
        //스폰 매니저에있는 랜덤 스폰 포인트 받기
        Debug.Log("Instantiated Player Controller");
        controller = PhotonNetwork.Instantiate(Path.Combine("PhotonPrefabs", "PlayerController"), spawnpoint.position, spawnpoint.rotation,0,new object[] { PV.ViewID});
        //포톤 프리펩에 있는 플레이어 컨트롤러를 저 위치에 저 각도로 만들어주기
        //포톤 뷰를 가지고있는 새로운 물체를 만들어주기
    }

    public void Die()
    {
        PhotonNetwork.Destroy(controller);
    }
}

```

* PlayerManager 스크립트의 전문이다.

---------------------------  
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;

public class SingleShotGun : Gun
//Gun으로부터 정보받기
{
    [SerializeField] Camera cam;
    PhotonView PV;
    void Awake()
    {
        PV = GetComponent<PhotonView>();  
    }
    public override void Use()
    {
        Debug.Log("using gun " + itemInfo.itemName);
        Shoot();
    }

    void Shoot()
    {
        Ray ray = cam.ViewportPointToRay(new Vector3(0.5f, 0.5f));
        //카메라로부터 레이저 발사
        ray.origin = cam.transform.position;
        //발사지점 카메라로부터
        if (Physics.Raycast(ray, out RaycastHit hit))
        {
            hit.collider.gameObject.GetComponent<IDamageable>()?.TakeDamage(((GunInfo)itemInfo).damage);
            //레이저에 맞은 물체가 데미지를 입을 수 있는 물체인가? 맞다면 아이템 정보에 맞는 데미지 받기
            PV.RPC("RPC_Shoot", RpcTarget.All, hit.point,hit.normal );
            //맞추면 상대방한테 맞았다고 알려주기
        }
    }

    [PunRPC]//Pun Remote Procedure Call의 약자로써 원격제어를 통해 함수를 실행시키는 기능이다.  
    void RPC_Shoot(Vector3 hitPosition,Vector3 hitNoraml) 
    {//hitNormal은 맞는 면이 향하는 방향이다.  
        Instantiate(bulletImpactPrefab, hitPosition+hitNoraml*0.001f, Quaternion.LookRotation(hitNoraml,Vector3.up)*bulletImpactPrefab.transform.rotation);
    }   //hitPosition+hitNormal*0.001f를 해주면 총알 자국이 대상표면 보다 아주 살짝 위에있게되서 겹쳐보이지 않는다. 
}

```

* SingleShotGun 스크립트의 전문이다.

---------------------------  
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public abstract class Gun : Item//아이템 스크립트에서 정보받아옴
{
    public abstract override void Use();
    //부모 클래스인 item에서 사용한 Use를 override해서 재정의
    public GameObject bulletImpactPrefab;
    //맞은 자국 만들기
}

```

* Gun 스크립트의 전문이다.

---------------------------  

[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture6-6.md)  
-----------------------------   
