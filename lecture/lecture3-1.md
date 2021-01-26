메뉴 만들기
=======================
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-1.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-2.PNG" width="50%">  

* 캔버스를 추가해주고 설정을 조절해준다.  

 ---------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-3.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-4.PNG" width="50%">  

* Launcher 스크립트를 추가해주고 그림과 같이 코드를 짜준다.   
* Photon에서 주는 기능들이 복잡한 과정을 그냥 코드 몇줄로 사용 가능하게 해준다.  

 ---------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-5.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-6.PNG" width="50%">  

* 아까 추가한 캔버스에 스크립트를 넣고 실행시켜보자.  
* 마스터 서버에 연결하고 로비에 진입했다고 디버그로그가 뜬다.  

 ---------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-7.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-8.PNG" width="50%">  

* 캔버스에 gameObject를 만들어주고 loadingMenu라고 해주자.   
* 크기 조절을 해주자.  

 ---------------------------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-10.PNG" width="50%">  

* 카메라 설정으로 가서 배경색을 회색으로 해주자.  
 ---------------------------------   
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-9.png" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-11.png" width="50%">  

* loadingMenu에 text mesh pro를 넣어준다.  
* 크기 조절과 가운데 정렬을 해주고 loading 적어준다.   

 ---------------------------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-12.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-13.PNG" width="50%">  

* 빈 gameObject에 title menu를 만들어주고 그안에 button container를 만들어준다.  
* button container에 vertical layout group을 추가해주고 크기를 조절하자. 
* vertical layout group이 있으면 저안에 있는 요소들은 알아서 상하 정렬이 된다.  

 ---------------------------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-14.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-15.PNG" width="50%">  

* Button container안에 넣을 버튼을 만들어준다.  
* 크기조절들을 해준다.  

 ---------------------------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-16.PNG" width="50%">  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-17.PNG" width="50%">  

* 버튼의 이름들과 텍스트들을 수정해준다.   

 --------------------------------- 
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-18.PNG" width="50%">  

* Menu와 MenuManager 스크립트를 만들어준다.  

 ---------------------------------  
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-19.PNG" width="50%"> 
<img src="https://github.com/isp829/3dunitymulty/blob/master/images/lecture3/lecture3-1/3-1-20.PNG" width="50%">  

* 각각 메뉴를 열고 닫는 스크립트와 어떤 메뉴를 열고 닫을지 결정하는 스크립트다.  
* 어려운 내용 없으니 넘어가자

---------------------------------
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Menu : MonoBehaviour
{
    public string menuName;
    public bool open;
    public void Open()
    {
        open = true;
        gameObject.SetActive(true);//특정 메뉴 켜지기
    }

    public void Close()
    {
        open = false;
        gameObject.SetActive(false);
    }
}
```
 
* Menu의 스크립트 전문이다.

----------------------------
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MenuManager : MonoBehaviour
{
    public static MenuManager Instance;//다른 class에서도 호출가능

    [SerializeField] Menu[] menus;//SerializedField를 사용하면 우리는 public처럼 쓸 수 있지만  public이 아니여서 외부에서는 못만짐.

    private void Awake()
    {
        Instance = this;
    }

    public void OpenMenu(string menuName)
    {
        for (int i = 0; i < menus.Length; i++)
        {
            if (menus[i].menuName == menuName)//string을 받아서 해당이름 가진 메뉴를 여는 스크립트
            {
                OpenMenu(menus[i]);
            }
            else if (menus[i].open)
            {
                CloseMenu(menus[i]);
            }
        }
    }

    public void OpenMenu(Menu menu)
    {
        for (int i = 0; i < menus.Length; i++)
        {
            if (menus[i].open)
            {
                CloseMenu(menus[i]);
            }
        }
        menu.Open();
    }

    public void CloseMenu(Menu menu)
    {
        menu.Close();
    }
}

```
* MenuManager의 스크립트 전문이다.   
----------------------------  
[목차로](https://github.com/isp829/3dunitymulty/blob/master/README.md)  
[다음](https://github.com/isp829/3dunitymulty/blob/master/lecture/lecture3-2.md)  
-----------------------------
