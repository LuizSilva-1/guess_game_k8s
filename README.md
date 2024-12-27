# Guess Game - Kubernetes Implementation

## **Jogo de Adivinhação com Flask**

Este projeto é parte do desafio da pós-graduação da PUC Minas, no curso de Containers e Orquestração. O objetivo é implementar a aplicação Guess Game utilizando Kubernetes, seguindo os conceitos de micro serviços. A aplicação é composta por:

- **Frontend**: Criado com React.
- **Backend**: Desenvolvido em Flask.
- **Banco de Dados**: Utilizando PostgreSQL.
- **NGINX**: Configurado como proxy reverso e balanceador de carga.

O jogador deve adivinhar uma senha gerada aleatoriamente, e o sistema fornece feedback sobre o número de letras corretas e suas respectivas posições.

---

## **Funcionalidades**

- Criação de um novo jogo com uma senha fornecida pelo usuário.
- Adivinhe a senha e receba feedback se as letras estão corretas e/ou em posições corretas.
- As senhas são armazenadas utilizando base64.
- As adivinhações incorretas retornam uma mensagem com dicas.

---

## **Requisitos**

- Kubernetes
- Docker
- Git

---

## **Instalação**

### **1. Clonar o Repositório**

```bash
git clone https://github.com/luizfersilva/guess_game_k8s.git
```

Navegue até a pasta do projeto:

```bash
cd guess_game_k8s
```

### **2. Subir a Aplicação**

Para subir a aplicação, use o comando abaixo:

```bash
kubectl apply -f guess_game_k8s/ -R
```

Verifique se todos os pods estão com o status `Running`:

```bash
kubectl get pods
```

### **3. Acessar a Aplicação**

Encaminhe a porta local para acessar o frontend:

```bash
kubectl port-forward svc/frontend 30080:80
```

Acesse a aplicação via navegador na URL:

```plaintext
http://localhost:30080
```

---

## **Como Jogar**

### **1. Criar um Novo Jogo**

- Acesse a URL do frontend.
- Digite uma frase secreta.
- Envie a frase.
- Salve o `game-id` gerado.

### **2. Adivinhar a Senha**

- Vá até a interface do jogo.
- Insira o `game-id` gerado na criação do jogo.
- Tente adivinhar a senha fornecendo suas tentativas.
- O sistema fornecerá feedback indicando letras corretas e suas posições.

---

## **Estrutura do Projeto**
```plaintext
k8s/
├── configmaps
│   ├── app-config.yaml
│   └── nginx-conf.yaml
├── db-credentials.yaml
├── deployments
│   ├── backend-deployment.yaml
│   ├── frontend-deployment.yaml
│   └── postgres-deployment.yaml
├── hpa
│   └── hpa-backend.yaml
├── services
│   ├── backend-service.yaml
│   ├── frontend-service.yaml
│   └── postgres-service.yaml
└── volumes
    ├── postgres-pv.yaml
    └── postgres-pvc.yaml
```

---

## **Configurações dos Arquivos**

### 1. **`nginx-conf.yaml`**
- Configurações para o NGINX, incluindo o proxy reverso e o balanceamento de carga para o backend.

### 2. **`app-config.yaml`**
- Configurações para os serviços backend e frontend, incluindo variáveis de ambiente e URLs de conexão.

### 3. **`db-credentials.yaml`**
- Secret contendo as credenciais sensíveis para o banco de dados PostgreSQL.

### 4. **Backend Deployment (`backend-deployment.yaml`)**
- Configura o backend com:
  - Variáveis de ambiente via `ConfigMap` e `Secret`.
  - Health checks configurados para garantir disponibilidade.
  - Suporte ao autoscaler configurado pelo HPA.

### 5. **Frontend Deployment (`frontend-deployment.yaml`)**
- Configura o frontend para consumir o backend e exibir a interface gráfica.
- Usa `ConfigMap` para definir a URL do backend.

### 6. **PostgreSQL Deployment (`postgres-deployment.yaml`)**
- Configura o banco de dados com persistência via PVC.
- Define credenciais através de `Secret`.

### 7. **HPA (`hpa-backend.yaml`)**
- Configura o HPA para o backend, baseado no uso de CPU:
  - Mínimo de réplicas: 1.
  - Máximo de réplicas: 10.
  - Utilização média-alvo de CPU: 70%.

### 8. **`backend-service.yaml`, `frontend-service.yaml` e `postgres-service.yaml`**
- Serviços Kubernetes para expor os pods no cluster.
- O frontend usa `NodePort` para permitir acesso externo.
- O backend e o PostgreSQL usam `ClusterIP` para comunicação interna.

### 9. **Volumes (`postgres-pv.yaml` e `postgres-pvc.yaml`)**
- Configuram o armazenamento persistente para o banco de dados PostgreSQL:
  - **PersistentVolume**: Define a capacidade de 5Gi e a política de retenção.
  - **PersistentVolumeClaim**: Solicita o volume persistente configurado.

---

## **Melhorias Implementadas**

- Utilização de imagens (`luizfersilva/backend:latest` e `luizfersilva/frontend:latest`) disponíveis no DockerHub.
- Pull de imagem configurado para garantir execução atualizada.
- Utilização de StatefulSet para o banco de dados PostgreSQL.
- HPA configurado para o backend, garantindo escalabilidade com base no uso de CPU.
- Volume persistente para o PostgreSQL por meio de PV e PVC.
- Balanceamento de carga configurado no NGINX.

---

## **Em Caso de Alterações no Código**

Qualquer alteração no código pode ser aplicada executando os comandos abaixo:

```bash
kubectl apply -f guess_game_k8s/ -R
```

---

## **Para Finalizar a Aplicação**

Para deletar todos os recursos criados:

```bash
kubectl delete -f guess_game_k8s/ -R
```
