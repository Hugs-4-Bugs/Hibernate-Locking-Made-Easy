## Hibernate Locking in very easy way

Imagine you and your friend are both trying to edit the same Google Doc at the same time. If you both make changes and save at the same time, one of you might accidentally overwrite the other’s changes. 

That’s exactly what happens in a database when multiple threads try to update the same record.

To prevent this, Hibernate provides locking mechanisms, just like Google Docs has ways to prevent conflicts. Let’s break it down super easy:

---

## 🔹 Two Main Types of Locking in Hibernate

### 1️⃣ Optimistic Locking (Hope for the Best 😃)

- Hibernate assumes there won’t be conflicts.
- If two people try to update the same record, the one who saves first wins.
- The second one gets an error and must retry.

### 🔹 Example (Using `@Version`)

```java
@Entity
public class BankAccount {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Version  // This is the magic!
    private int version;
    
    private int balance;

    // Getters and setters...
}
```

### 🔹 How it works?
1️⃣ Hibernate adds a hidden version number to each row.  
2️⃣ When someone updates the row, Hibernate checks if the version number is the same.  
3️⃣ If another update has already happened, it fails and asks to try again.

📌 **Real-life example:**
You open a Google Doc, and your friend edits it before you save. When you try to save, Google warns you that the document changed, so you need to refresh and try again.

### Optimistic Locking Example (Fast, but conflicts can happen)

🔹 **Steps:**  
✅ Add `@Version` to the entity.  
✅ Hibernate checks if the version matches before updating.  
✅ If another update happens first, an error occurs, and you must retry.

```java
@Entity
public class BankAccount {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Version  // Optimistic Locking
    private int version;
    
    private int balance;
    
    // Getters and setters...
}
```

🛠️ **Service: Update Account Balance**

```java
@Transactional
public void updateBalance(Long accountId, int amount) {
    BankAccount account = repository.findById(accountId).orElseThrow();
    
    // Simulate delay (mimic multiple users updating)
    try { Thread.sleep(3000); } catch (InterruptedException e) {}

    account.setBalance(account.getBalance() + amount);
    repository.save(account);
}
```

⏳ If two people update the balance at the same time, one will fail and need to retry!

---

### 2️⃣ Pessimistic Locking (Don't Trust Anyone! 😠)

- Hibernate locks the record while someone is working on it.
- No one else can edit it until the lock is released.

### 🔹 Example (Using `PESSIMISTIC_WRITE`)

```java
BankAccount account = entityManager.find(BankAccount.class, 1L, LockModeType.PESSIMISTIC_WRITE);
account.setBalance(account.getBalance() + 100);
entityManager.merge(account);
```

### 🔹 How it works?
1️⃣ When one person updates the record, Hibernate locks it.  
2️⃣ Others have to wait until the first person finishes.  
3️⃣ Prevents conflicts but can slow down the system.

📌 **Real-life example:**
Imagine you go to an ATM to withdraw money. The ATM locks your account while you take cash out. Nobody else can withdraw from your account at that moment.

### Pessimistic Locking Example (Safe, but slow)

🔹 **Steps:**  
✅ Hibernate locks the record so no one else can edit it.  
✅ Other updates must wait until the lock is released.

🛠️ **Lock Account Before Updating**

```java
@Transactional
public void updateBalance(Long accountId, int amount) {
    BankAccount account = entityManager.find(BankAccount.class, accountId, LockModeType.PESSIMISTIC_WRITE);
    
    // Update balance
    account.setBalance(account.getBalance() + amount);
    
    entityManager.merge(account);
}
```

🔒 Now, if two people try to update at the same time, one must wait for the other to finish!

---

## 🔹 When to Use What?

| Situation | Use Optimistic Locking 😃 | Use Pessimistic Locking 😠 |
|-----------|--------------------------|--------------------------|
| Few conflicts expected | ✅ Best choice! | ❌ Slows things down |
| Many people update the same data | ❌ Can cause failures | ✅ Prevents data loss |
| Performance matters | ✅ Very fast | ❌ Slower |

---

## 🔹 Conclusion:

- **Optimistic Locking**: Fast, but conflicts can happen.
- **Pessimistic Locking**: Safer, but slower because it locks records.
- **Choose based on how often updates happen in your app! 🚀**




---


## Click here to get in touch with me: 
<a href="https://github.com/Tech-Hubs" target="_blank"><b>PrabhatDevLab</b></a>, 
<a href="https://hugs-4-bugs.github.io/myResume/" target="_blank"><b>PrabhatKumar.com</b></a>, 
<a href="https://www.linkedin.com/in/prabhat-kumar-6963661a4/" target="_blank"><b>LinkedIn</b></a>, 
<a href="https://stackoverflow.com/users/19520484/prabhat-kumar" target="_blank"><b>Stackoverflow</b></a>, 
<a href="https://github.com/Hugs-4-Bugs" target="_blank"><b>GitHub</b></a>, 
<a href="https://leetcode.com/u/Hugs-2-Bugs/" target="_blank"><b>LeetCode</b></a>, 
<a href="https://www.hackerrank.com/profile/Prabhat_7250" target="_blank"><b>HackerRank</b></a>, 
<a href="https://www.geeksforgeeks.org/user/stealthy_prabhat/" target="_blank"><b>GeeksforGeeks</b></a>, 
<a href="https://hugs-4-bugs.github.io/AlgoByPrabhat/" target="_blank"><b>AlgoByPrabhat</b></a>, 
<a href="http://hugs-4-bugs.github.io/Sharma-AI/" target="_blank"><b>SHARMA AI</b></a>,  <a href="https://linktr.ee/_s_4_sharma" target="_blank"><b>About Me</b></a>, <a href="https://www.instagram.com/_s_4_sharma/" target="_blank"><b>Instagram</b></a>, <a href="https://x.com/kattyPrabhat" target="_blank"><b>Twitter</b></a>

