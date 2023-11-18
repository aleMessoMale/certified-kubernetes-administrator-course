# Application Failure
  
  - Take me to [Lecture](https://kodekloud.com/topic/application-failure/)

  - In this lecture we will go step by step in troubleshooting Application failure.

  - To check the Application/Service status of the webserver

    ```
    curl http://web-service-ip:node-port
    ```

    ![app](../../images/app.PNG)

  - To check the endpoint of the service and compare it with the selectors
    - ossia, controlla le labels del pod e verifica che corrispondano con quelle su cui il service
    utilizza il selector

      ```
      kubectl describe service web-service
      ```   

      ![svc](../../images/svc.PNG)


  - To check the status and logs of the pod (se ci son tanti restart p.es. o se non è Running)

    ```
    kubectl get pod
    ```

    ```
    kubectl describe pod web
    ```

    ```
    kubectl logs web
    ```

  - Per stare in tail sui logs: 

    ```
    kubectl logs web -f 
    ```
    
  - To check the logs of the previous pod

    ```
    kubectl logs web -f --previous
    ```
    
    ![db](../../images/db.PNG)


  #### Hands on Labs

  - Lets troubleshoot the [Application](https://kodekloud.com/topic/practice-test-application-failure/)