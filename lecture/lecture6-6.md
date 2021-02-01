사용할 아이템 만들기      
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-6/6-6-1.PNG" width="100%">  

* 두개를 실행시켜서 해보면 플레이어는 총에 맞아서 사라졌는데 총알 자국만 공중에 둥둥 떠다니는걸 볼 수 있다.  
* 어떻게 해야될까?    

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-6/6-6-2.PNG" width="100%">  

* Single Shot Gun 스크립트를 열어서 수정해주자.   
* Physics.OverlapSphere를 사용하여 총알자국 근처에있는 Collider를 반환해서 부모로 삼게 해주자.  
* 또 총알 자국이 그냥 10초 지나면 알아서 사라지게 해서 총알 자국이 영원히 못남게 해주자.  

----------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture6/lecture6-6/6-6-3.PNG" width="100%">  

* 실행해보면 이제 플레이어가 GameOver되서 사라지면 총알 자국도 바로사라진다.  

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

----------------------------------------------   
[목차로](https://github.com/isp829/Unity3DMulti/blob/master/README.md)  
[다음](https://github.com/isp829/Unity3DMulti/blob/master/lecture/lecture7-1.md)  
-----------------------------   
