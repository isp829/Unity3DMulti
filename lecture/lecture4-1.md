룸/플레이어 매니저 만들기 
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-1.PNG" width="50%">   

* 룸/플레이어 매니저를 만들어주자.  
* 룸매니저가 룸에 들어온 사람만큼 Player Manager를 만들고  
Player Manager가 각자의 Player Controller를 만들어서 캐릭터를 조종할 수 있게 만들어보자. 

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-2.PNG" width="50%">   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-3.PNG" width="50%">   

* Room Manager를 만들어주고 Room Manager스크립트를 만들어주자.  

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-4.PNG" width="50%">   

* 방에 룸매니저가 없으면 하나 만들어주고 게임이 시작되면 Player Manager를 생성하도록 코드를 작성해주자.  

--------------------------------   
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Photon.Pun;
using UnityEngine.SceneManagement;
using System.IO;//Path사용위에 사용

public class RoomManager : MonoBehaviourPunCallbacks//다른 포톤 반응 받아들이기
{
    public static RoomManager Instance;//Room Manager 스크립트를 메서드로 사용하기 위해 선언

    void Awake()
    {
        if (Instance)//다른 룸매니저 존재확인
        {
            Destroy(gameObject);//있으면 파괴
            return;
        }
        DontDestroyOnLoad(gameObject);//룸매니저 나혼자면 그대로 
        Instance = this;
    }

    public override void OnEnable()
    {
        base.OnEnable();
        SceneManager.sceneLoaded += OnSceneLoaded;
        // 활성화되면 씬 매니저의 OnSceneLoaded에 체인을 건다.
        // 씬이 바뀔때마다 작동됨
    }

    public override void OnDisable()
    {
        base.OnDisable();
        SceneManager.sceneLoaded -= OnSceneLoaded;
        // 비활성화되면 씬 매니저의 체인을 지운다.
    }

    void OnSceneLoaded(Scene scene, LoadSceneMode load)
    {
        if (scene.buildIndex == 1)//게임씬이면. 0은 현재 시작메뉴 씬이다. 
        {
            PhotonNetwork.Instantiate(Path.Combine("PhotonPrefabs", "PlayerManager"), Vector3.zero, Quaternion.identity); ;
            //포톤 프리펩에 있는 플레이어 매니저를 저 위치에 저 각도로 만들어주기
        }
    }
}

```

* Room Manager스크립트의 전문이다.  

---------------------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-5.png" width="50%">   

* 룸매니저에 스크립트를 넣어주고 PhotonView를 넣어주자.  
* 중복되는 ID가 없게 999넣어주자.  

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-6.PNG" width="50%">   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-7.PNG" width="50%">   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-8.PNG" width="50%">   

* Player Manager를 만들어주고 Photon View를 넣어준다.  
* Resources->PhotonPrefabs 폴더를 만들어주고 그안에 Player Manager를 프리펩화 시켜준다.  
* 프리펩화 시켜줬으면 scene에서는 삭제해준다. 

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-9.PNG" width="50%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-10.PNG" width="50%">   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-11.PNG" width="50%"><img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-12.PNG" width="50%">   

* 빌드해보고 실행한후 게임을 시작하면 Player Manager가 두개가 된다.  
* Player Manager들을 확인하면 각각 주인이 정해져있는걸 볼 수 있다.  

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-13.png" width="50%">   

* Photon에서 기본으로 제공되는 예제들에 PlayerManager의 이름을 가진 스크립트가 존재하므로 Demos들을 지워준다.  

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-14.PNG" width="50%">   

* PlayerManager 스크립트를 만들어준다.    

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-15.PNG" width="50%">   

* 간단하게 포톤뷰를 가지고 내 포톤뷰가 맞으면 로그를 보내도록 코드를 짜주자.  

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
    }
}

```

* PlayerManager스크립트의 전문이다.  

-----------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-16.PNG" width="50%">   

* PlayerManager 프리펩에 PlayerManager스크립트를 넣어준다.  

--------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-17.png" width="50%">   

* 실행해서 디버그를 보면 잘 싱행되는걸 알 수 있다.  

--------------------
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-18.PNG" width="50%">  

* 3D Object capsule을 하나 만들고 이름붙여주자.  

------------------------------------------------------       
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-19.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture4/lecture4-1/4-1-20.PNG" width="50%">  

* PlayerController안에 camera도 추가해주고 위치를 조절해준다.  
* 포톤 뷰도 추가해준다.  

------------------------------------------------------       
[목차로](https://github.com/isp829/3dunitymulty/blob/master/README.md)  
[다음](https://github.com/isp829/3dunitymulty/blob/master/lecture/lecture4-2.md)  
-----------------------------

    
