# LAB-03 - Podstawowy pipeline CI/CD z ręcznym deploymentem

## Opis

Projekt demonstruje podstawowy pipeline CI/CD zbudowany w GitHub Actions,
który automatycznie buduje, testuje i publikuje obraz Docker do Azure Container Registry.
Deployment do klastra Kubernetes wykonywany jest ręcznie.

## Architektura

```
GitHub Repo
    │
    │  (Build + Test + Push)
    ▼
Azure Container Registry  ──── (ręczny deployment) ────▶  Azure Kubernetes Service
```

## Technologie

- Python + Flask
- Docker
- GitHub Actions
- Azure Container Registry (ACR)
- Azure Kubernetes Service (AKS)

## Uruchomienie lokalne

```bash
pip install -r requirements.txt
python app.py
```

Lub przez Docker:

```bash
docker build -t app-local .
docker run -p 8080:8080 app-local
```

Aplikacja dostępna pod: http://localhost:8080

## Endpointy

| Endpoint  | Opis                        |
|-----------|-----------------------------|
| `/`       | Zwraca powitanie            |
| `/health` | Sprawdza stan aplikacji     |

## Testy

```bash
pytest test_app.py -v
```

## Pipeline CI/CD

Pipeline uruchamia się automatycznie przy każdym pushu do gałęzi `main` i wykonuje:

1. Checkout kodu
2. Uruchomienie testów jednostkowych
3. Logowanie do Azure Container Registry
4. Build obrazu Docker z tagiem SHA commita
5. Push obrazu do ACR

## Deployment do AKS

Po zakończeniu pipeline należy ręcznie zaktualizować obraz w klastrze:

```bash
kubectl set image deployment/app \
  app=wiktoriaacrlab03.azurecr.io/app:<git-sha>

kubectl rollout status deployment/app
```

## Struktura projektu

```
.
├── .github/
│   └── workflows/
│       └── ci.yml
├── k8s/
│   └── deployment.yaml
├── app.py
├── test_app.py
├── Dockerfile
├── requirements.txt
└── README.md
```