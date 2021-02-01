교란용 클론 만들기
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-1.PNG" width="50%">  

* 술래를 헷갈리게 하는 클론들을 만들어주자.  
* 플레이어와 같은 모양의 Capsule을 하나 만들어주고 이름을 Clone이라 해주자. 

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-2.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-3.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-4.PNG" width="50%">  

* Clone에 rigidbody, PhotonView, PhotonTransformView, NavMeshAgent를 넣어주자. 
* Rigidbody에서 xyz고정에 xyz축회전도 고정으로 해주자.  
* NavMeshAgent는 클론들이 순찰할때 도움을 준다.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-5.PNG" width="50%">  

* ClonePatrol스크립트를 만들어준다.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-6.PNG" width="50%">  

* SpawnManager에서 스폰포인트로 썼던 위치값을 순찰포인트로 해서 계속 순찰하게 코드를 작성해주자.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-8.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-7.png" width="50%">  

* Clone 태그를 붙여주고 프리펩화 시켜준다.  

----------------------------------------------   ]
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-9.png" width="50%">  

* Game Scene에서 지형으로 쓸 물체들을 다 선택해준다.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-10.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-11.png" width="50%">  

* Navigation에서 object와 Bake 설정을 다해주고 Bake 버튼을 눌러주자.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-12.PNG" width="50%">  

* 이제 NavMeshAgent가 걸어다닐 수 있는 범위가 색깔로 표현된다.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-13.PNG" width="50%">  

* Game Scene에 Clone들을 여러개 올려놓고 실행해보면 순찰을 잘하는걸 볼 수 있다.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-14.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-15.PNG" width="50%">  

* Single Shot Gun 스크립트를 수정해주자.  
* Clone들은 총맞으면 바로 사라지도록 코드를 수정해주자.  
* 또한 술래가 클론인척하는 사람을 맞추면 체력이 회복되도록 코드를 수정해주자.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-16.png" width="50%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-17.png" width="50%">    
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-18.PNG" width="50%">  

* PlayerController 프리펩에있는 Rifle과 Pistol에 shooter를 추가해주자.  
* PlayerController에 Player태그를 추가해주자.  

----------------------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture8/lecture8-1/8-1-19.PNG" width="50%">  

* 실행해보면 총에 맞으면 Clone들은 사라지고 Player를 맞추면 체력이 회복되는걸 볼 수 있다.  

----------------------------------------------   
```
using Photon.Pun;
using Photon.Realtime;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;

public class ClonePatrol : MonoBehaviour
{
    private NavMeshAgent agent;
    //순찰을 위한 네비게이션 메쉬 에이전트
    PhotonView PV;
    //위치 동기화욤 PV
    void Awake()
    {
        PV = GetComponent<PhotonView>();
        agent = GetComponent<NavMeshAgent>();

    }
    void Start()
    {
        agent.autoBraking = false;
        //순찰 계속함
        GotoNextPoint();
    }
    void GotoNextPoint()
    {
        Transform Patrolpoint = SpawnManager.Instance.GetSpawnpoint();
        //스폰 포인트로 썼던 위치들을 순찰 포인트로 씀
        agent.destination = Patrolpoint.position;
        //네비게이션 다음 목적지를 랜덤 순찰포인트로
    }
    void Update()
    {
        if (!agent.pathPending && agent.remainingDistance < 0.5f)
            GotoNextPoint();
        //근처에가면 다음 목적지로
    }
}
```

* Clone Patrol 스크립트의 전문이다.  

------------------
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
    public GameObject shooter;
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
            if (hit.transform.tag == "Player")
            {
                shooter.GetComponent<PlayerController>()?.TakeDamage(-30);
                //음수값의 데미지를 입으니까 체력 회복이다.
            }
            if (hit.transform.tag == "Clone")
            {
                Destroy(hit.transform.gameObject);
            }
            PV.RPC("RPC_Shoot", RpcTarget.All, hit.point,hit.normal );
            //맞추면 상대방한테 맞았다고 알려주기
        }
    }

    [PunRPC]//Pun Remote Procedure Call의 약자로써 원격제어를 통해 함수를 실행시키는 기능이다.  
    void RPC_Shoot(Vector3 hitPosition,Vector3 hitNoraml) 
    {//hitNormal은 맞는 면이 향하는 방향이다.  
        Collider[] colliders = Physics.OverlapSphere(hitPosition, 0.3f);
        //0.3f 반경내에 collider들을 검출한다. 
        if (colliders.Length != 0)//콜라이더가 존재한다면
        {
            GameObject bulletImpactObj=Instantiate(bulletImpactPrefab, hitPosition + hitNoraml * 0.001f, Quaternion.LookRotation(hitNoraml, Vector3.up) * bulletImpactPrefab.transform.rotation);
            //hitPosition+hitNormal*0.001f를 해주면 총알 자국이 대상표면 보다 아주 살짝 위에있게되서 겹쳐보이지 않는다. 
            Destroy(bulletImpactObj, 10f);
            //총알 자국은 10초후에는 사라진다.  
            bulletImpactObj.transform.SetParent(colliders[0].transform);
            //주변에 있는 콜라이더를 부모로 설정한다.  
            //부모가 사라지면 자식물체들도 다 사라지므로 총알자국만 둥둥떠다니는걸 해결 할 수 있다.  
        }
    }
        
}
```

* Single Shot Gun 스크립트의 전문이다.  

---------------------------   
[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture9-1.md)  
-----------------------------   
