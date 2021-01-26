룸/플레이어 매니저 만들기 
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-2/4-2-1.PNG" width="50%">   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-2/4-2-2.PNG" width="50%">   

* PlayerController를 프리펩화 시켜주고 PlayerManager스크립트를 수정해준다.   

--------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;
using System.IO;//path사용위해

public class PlayerManager : MonoBehaviour
{
    PhotonView PV;//포톤뷰 선언

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
        Debug.Log("Instantiated Player Controller");
        PhotonNetwork.Instantiate(Path.Combine("PhotonPrefabs", "PlayerController"), Vector3.zero, Quaternion.identity);
        //포톤 프리펩에 있는 플레이어 컨트롤러를 저 위치에 저 각도로 만들어주기
    }
}

```

* 수정한 PlayerManager스크립트의 전문이다.  

---------------------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-2/4-2-3.PNG" width="50%">   

* 실행해보면 PlayerManager하나당 PlayerController가 하나씩 만들어지는걸 볼 수 있다.  

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-2/4-2-4.PNG" width="50%">   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-2/4-2-5.png" width="50%">   

* 로그를 보면 audio listener가 2개있다고 뜬다.  
* Game Scene에서 메인카메라의 audio listener를 해제해 주자.  

------------------------------------------------------       
[목차로](https://github.com/isp829/3dunitymulty/blob/master/README.md)  
[다음](https://github.com/isp829/3dunitymulty/blob/master/lecture/lecture5-1.md)  
-----------------------------
