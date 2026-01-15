# Things about K8S you must know!

## SSL/TLS Configuration in Ingress:  
You need to create a secret of type tls and provide the tls.crt and tls.key in the data block.  
<img width="429" height="310" alt="image" src="https://github.com/user-attachments/assets/85d497de-fdee-430c-9b4f-9a76f0163929" />  

Configure it in the Ingress.yml file as below:  
<img width="695" height="790" alt="image" src="https://github.com/user-attachments/assets/783e86b3-bed4-4a03-9c27-4b5c220c95e8" />

**Flow of the HTTPS Request**  
<img width="607" height="343" alt="image" src="https://github.com/user-attachments/assets/5f8d04d5-21f3-4269-bcb0-f47c95c6520c" />

