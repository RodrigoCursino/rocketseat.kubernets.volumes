## 📦 Introdução aos Volumes

### 🌐 Conceito
No Kubernetes (assim como no Docker), containers são **efêmeros**, ou seja:
- Quando o container (ou pod) é reiniciado ou removido, **tudo o que está dentro dele é perdido**.
- Em aplicações **stateless** (sem estado), isso é aceitável.
- Em aplicações **stateful** (com estado), **precisamos preservar dados**.

### 💾 Quando usar Volumes
Volumes são necessários quando:
- A aplicação precisa **armazenar dados persistentes**, como:
  - Bancos de dados (MySQL, PostgreSQL, etc.)
  - Logs
  - Arquivos temporários ou assets gerados pela aplicação
- Você precisa garantir que os dados sobrevivam mesmo que o pod seja recriado.

Exemplo:  
> Se um banco de dados for executado em um pod e esse pod cair, os dados seriam perdidos sem um volume persistente.

---

## ⚙️ StatefulSet
- Recurso usado para aplicações **stateful** no Kubernetes.
- Garante **ordem**, **identidade estável** e **persistência**.
- Exemplo: banco de dados com **líder** e **followers**.

> Será abordado no módulo avançado de Kubernetes.

---

## 📂 Storage Class

### 🔍 O que é
Um **StorageClass** define **como** o armazenamento será provisionado no cluster.  
Ele é o **elo entre o Kubernetes e o provedor de armazenamento** (local ou em nuvem).

Exemplos de provisionadores:
- **AWS EBS (Elastic Block Store)**
- **Azure Disk**
- **vSphere / Cinder (OpenStack)**
- **Local Path** (ambientes locais)

> No caso do Kind (Kubernetes local), o provisionador padrão é o **local-path**.

### 🧩 Estrutura
Comando para listar as Storage Classes disponíveis:
```bash
kubectl get storageclass
# ou
kubectl get sc
```
---

# 📦 Volumes no Kubernetes

## 🧩 Contexto

Depois de compreender o conceito de **StorageClass**, o próximo passo é entender **Volumes**, que são essenciais para gerenciar **dados persistentes** em aplicações dentro de um cluster Kubernetes.

Por padrão, containers são **efêmeros** — ou seja, quando um pod é encerrado, todos os dados armazenados localmente nele são perdidos.  
Volumes resolvem esse problema ao permitir **persistência de dados**.

---

## 🔹 StorageClass e Volume

- O **StorageClass** define **como** o armazenamento será provisionado (qual provedor, tipo de disco, política de retenção etc.).
- O **Volume** é o **espaço reservado** de fato — a alocação concreta de armazenamento, feita com base nas definições do StorageClass.

📘 Analogia:
> O StorageClass é o “modelo” de armazenamento.  
> O Volume é a “instância real” desse modelo.

---

## ⚙️ Tipos de Volumes

### 1. Volume Efêmero (Ephemeral Volume)
- Dados **não persistem** após o ciclo de vida do pod.
- São temporários e descartados quando o container é reiniciado ou removido.
- Exemplo: `emptyDir`
  ```yaml
  volumes:
    - name: temp-storage
      emptyDir: {}

---

Claro! Aqui está o conteúdo formatado em **Markdown (.md)** com as anotações principais dessa transcrição sobre **Persistent Volume Claim (PVC)** no Kubernetes:

---

# 📘 Kubernetes — Associação de Volumes (PVC)

## 🔹 Conceito Geral

Nesta etapa, o foco é entender **como a aplicação (pod ou deployment)** se associa a um **volume persistente** no Kubernetes.

### Estrutura Hierárquica

1. **StorageClass**

   * Define o **provisionador** (provisioner) que será responsável por criar e gerenciar volumes.
   * No exemplo citado, o provisionador é o `local-path`.
   * Representa o tipo de armazenamento (local, em nuvem, etc).

2. **PersistentVolume (PV)**

   * É a **reserva de espaço em disco** feita pelo cluster (local ou externo).
   * Funciona como um disco físico reservado para uso futuro.
   * Exemplo: reservar 10 GB de espaço.

3. **PersistentVolumeClaim (PVC)**

   * É o **requerimento de uso de uma parte do PV** por uma aplicação.
   * A aplicação **não conversa diretamente com o PV**, mas sim com o **PVC**.
   * O PVC define **quanto espaço** do volume será utilizado.
   * Exemplo: de um PV de 10 GB, o PVC pode requisitar 1 GB.

4. **Aplicação (Pod/Deployment)**

   * Faz a associação ao **PVC**, e **não diretamente ao PV**.
   * O PVC é o intermediário que conecta o pod ao volume persistente.

---

## 🔹 Relação Entre os Componentes

| Componente                      | Função                                      | Relação                          |
| ------------------------------- | ------------------------------------------- | -------------------------------- |
| **StorageClass**                | Define o provisionador (quem cria o volume) | Gerencia o tipo de armazenamento |
| **PersistentVolume (PV)**       | Reserva de espaço (disco)                   | Associado ao StorageClass        |
| **PersistentVolumeClaim (PVC)** | Pedido de espaço (claim)                    | Associado ao PV                  |
| **Pod/Deployment**              | Usa o volume                                | Associado ao PVC                 |

---

## 🔹 Comportamento `WaitForFirstConsumer`

* O **binding (vinculação)** entre o PVC e o PV ocorre **somente quando há um consumidor ativo**, ou seja, quando o **pod é criado**.
* Isso evita alocação desnecessária de espaço.
* O status do PVC muda para `Bound` **após a aplicação estar rodando**.

---

## 🔹 Exemplo Prático (Fluxo Resumido)

1. Criar o **StorageClass**
   → Define o provisionador e o tipo de armazenamento.

2. Criar o **PersistentVolume (PV)**
   → Reserva um espaço de disco (ex: 10 GB).

3. Criar o **PersistentVolumeClaim (PVC)**
   → Requisita parte do PV (ex: 1 GB).

4. Associar o **PVC ao Deployment/Pod**
   → A aplicação consome o volume através do claim.

---

## 🔹 Resumo Conceitual

* **StorageClass:** define como e onde o volume será criado.
* **PV:** representa o espaço físico reservado.
* **PVC:** representa a requisição de espaço pela aplicação.
* **Pod:** utiliza o PVC para acessar o armazenamento persistente.

---

## 🔹 Próximos Passos (aula prática)

Na sequência:

* Criar os objetos YAML correspondentes:

  * `StorageClass`
  * `PersistentVolume` (PV)
  * `PersistentVolumeClaim` (PVC)
* Testar o vínculo desses objetos com uma aplicação real no cluster.
