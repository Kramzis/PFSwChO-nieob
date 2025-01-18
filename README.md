# SPRAWOZDANIE Z LABORATORIUM PROGRAMOWANIE FULL-STACK W CHMURZE OBLICZENIOWEJ

## Kramek Magdalena

## Odpowiedzi do podpunktu A

W przypadku Deploymentu jest on przeznaczony do zarządzania aplikacjami, które są bezstanowe (ang. stateless).
Gdy Deployment zostanie zeskalowany "w górę" nowo utworzone repliki są niezależne od siebie, a ich instancje równoważne.
Natomiast podczas skalowania "w dół" dowolne z replik mogą być usunięte.

Natomiast StatefulSet służuy do zarządzania aplikacjami stanowymi (ang. stateful). Aplikacje stanowe wymagają, aby poszczególne instancje były zachowywane, np. bazy danych.
Podczas gdy StatefulSet zostanie przeskalowany "w górę" powstające repliki mają swoje unikalne nazwy oraz są tworzone w określonej kolejności.
Zachowana kolejność w przypadku skalowania "w dół" jest wykorzystywana, by ostatnia stworzona replika została usunięta jako pierwsza, przedostania jako druga itd.

## Pliki konfiguracyjne

- **Plik:** `secret4db.yaml`
	- Zawiera dane uwierzytelniające do bazy danych. Jest to definicja sekretu Kubernetesa.
- **Plik** `service4db.yaml`
	- Zawiera definicję serwisu, który umożliwia komunikowanie się z bazą danych w klastrze.
- **Plik** `stateful4db.yaml`
	- Zawiera konfigurację aplikacji MySQL.

## Uruchomienie

Należy uruchomić minikube'a oraz wywołać w konsoli te polecenia:

```bash
kubectl apply -f secret4db.yaml
kubectl apply -f service4db.yaml
kubectl apply -f stateful4db.yaml
```
## Potwierdzenie poprawności działania

Na początek należy sprawdzić stan podów i zweryfikować działanie StatefulSetu.

```
bash
                                                                           
┌──(kali㉿kali)-[~]
└─$ kubectl get pods       
NAME      READY   STATUS    RESTARTS   AGE
mysql-0   1/1     Running   0          56s
mysql-1   1/1     Running   0          16s
mysql-2   1/1     Running   0          12s
```
```
bash
┌──(kali㉿kali)-[~]
└─$ kubectl get statefulset
NAME    READY   AGE
mysql   3/3     116s
```

- **Skalowanie w dół**
- ```
bash
┌──(kali㉿kali)-[~]
└─$ kubectl scale statefulset mysql --replicas=2
statefulset.apps/mysql scaled

┌──(kali㉿kali)-[~]
└─$ kubectl get pods                            
NAME      READY   STATUS    RESTARTS   AGE
mysql-0   1/1     Running   0          4m52s
mysql-1   1/1     Running   0          4m12s
```
- **Skalowanie w górę**
- ```
bash
┌──(kali㉿kali)-[~]
└─$ kubectl scale statefulset mysql --replicas=4
statefulset.apps/mysql scaled

┌──(kali㉿kali)-[~]
└─$ kubectl get pods
NAME      READY   STATUS              RESTARTS   AGE
mysql-0   1/1     Running             0          8m43s
mysql-1   1/1     Running             0          8m3s
mysql-2   1/1     Running             0          23s
mysql-3   1/1     Running             0          21s
mysql-4   1/1     Running             0          15s
```
