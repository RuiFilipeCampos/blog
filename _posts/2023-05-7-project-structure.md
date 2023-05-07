---
layout: post
title: My Journey Towards an Efficient Project Structure and the Road Ahead
date: 2023-05-7
author: Rui Campos
---

I ventured into the software industry just over a year ago. Since then, I've had the opportunity to work in various areas such as backend, frontend, embedded systems, devops, cloud, and machine learning applications. Throughout my experiences, I've learned the importance of structuring projects efficiently, which has led me to my current approach.

**Efficient Project Structure with Docker**

One of the key factors in my project organization is adhering to the single responsibility principle of Docker and Docker Compose. This method allows me to understand the application at a glance, access logs easily, and monitor container health effectively. To achieve this, I use the following setup:

- docker-compose.yml for production
- docker-compose.override.yml for development configurations
- .env for environment-specific settings

**Embracing the Django Way**

My journey led me to embrace the Django way of structuring projects, which, despite some initial hurdles, has proven to be extremely beneficial. By organizing my applications into several loosely coupled sub-applications, I've created a structure that is easy to understand, modify, and reuse between projects. This modular approach not only enhances code maintainability but also promotes reusability, making it easier to scale projects and collaborate with other developers.

Furthermore, this method of organization keeps related code close together, which streamlines the testing process. By grouping related functionality within the same sub-application, it becomes more straightforward to identify and test the relevant code. This localized testing approach reduces the likelihood of unintended side effects and helps ensure that individual components work as intended. As a result, the overall quality of the software is improved, and the development process becomes more efficient.

With the Django way of organizing projects, developers can enjoy the benefits of a clean, modular structure that supports easy testing, maintainability, and scalability. This approach fosters a more efficient development process and paves the way for successful collaboration and project outcomes.

**Tackling Code Coupling**

One of the primary challenges I faced was ensuring that code from different folders did not become highly coupled. To tackle this issue, I devised a strategy wherein each sub-application contains two files that can be imported by other sub-applications: schema.py and service.py. Furthermore, the only file that can import from other files at all are the service.py files. This approach effectively segregates code into entities and maintains a clean organization.

**Dependency Injection in Action**

One of the design patterns that I have found to work well within the way I like to code (simple, functional code) is dependency injection. For example, in the context of my Django project structure, I usually have a database service module that manages MySQL connections. To illustrate this, here's a simplified example of how I use dependency injection for a MySQL connection:

```pyhon

# database/service.py
from contextlib import contextmanager
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

engine = create_engine("mysql+mysqldb://user:password@localhost/dbname")
Session = sessionmaker(bind=engine)

def inject_session(func):
    def wrapper(*args, **kwargs):
        session = Session()
        try:
            result = func(session, *args, **kwargs)
            session.commit()
            return result
        except:
            session.rollback()
            raise
        finally:
            session.close()

    return wrapper

# user/service.py
import database.service
import user.table

@database.service.inject_session
def create_new_user(session, user_data):
    user = user.table.User(**user_data)
    session.add(user)
    session.flush()
    return user.id

```


In this example, `inject_session` is a wrapper function in the `database/service.py` file. It manages the creation and lifecycle of the MySQL session and ensures that the session is properly closed after usage. The `user/service.py` file imports `inject_session` and uses it as a decorator for the `create_new_user` function, which performs a database operation to create a new user.

**Avoiding the 'Core' or 'Settings' Trap**

In my journey, I've discovered that it's best to avoid having a folder like 'core' or 'settings' where base classes for inheritance would be kept. This type of folder may seem necessary, but in reality, it often leads to an unclear organization and can create confusion. Instead, when there's a need to extend the functionality of a framework I'm using, I prefer to create a sub-application named after the extension, such as 'celery_extension', 'fastapi_extension', etc. Each of these sub-applications has a service.py file, which serves as an interface for the extension.


**Future Evolution**

As I continue to explore new design patterns and work on more complex projects, I expect that the way I internally structure the sub-applications will change significantly. However, the high-level folder structure and sub-app interactions are likely to remain consistent. I'm particularly interested in data pipelines and finding ways to organize them into clean and maintainable code.

**Conclusion**

My journey in structuring projects efficiently has been a rewarding learning experience. By embracing the Django way and incorporating design patterns such as dependency injection, I've been able to create a project structure that is clean, maintainable, and scalable. As I continue to explore new patterns and tackle larger projects, I anticipate that the internal organization of sub-applications will evolve to better accommodate the increasing complexity. However, the overall principles of modularity and clean organization are likely to remain a cornerstone of my approach.

As developers, we should always be willing to learn and adapt our methods to better suit the needs of our projects. With the right mindset and a commitment to continuous improvement, we can create more efficient and effective project structures, ultimately leading to better software and more successful outcomes.




