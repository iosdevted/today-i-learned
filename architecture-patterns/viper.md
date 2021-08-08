# VIPER 

## 기본 구조

[VIPER-Template](https://github.com/zafarivaev/VIPER-Template)

## 역할 

- **Interactor** — contains business logic related to the data (**Entities**) or networking, like creating new instances of entities or fetching them from the server. For those purposes you’ll use some *Services* and *Managers* which are not considered as a part of VIPER module but rather an external dependency.

- **Presenter** — contains the UI related (but UIKit *independent*) business logic, invokes methods on the **Interactor**.

- **View** - 뷰의 기능만을 수행하게 만든다. 다른 모든 작업은 presenter나 interactor에게 맡긴다.

- **Entities** — your plain data objects, not the *data access layer,* because that is a responsibility of the **Interactor**.

- **Router** — responsible for the segues between the VIPER **modules**.

