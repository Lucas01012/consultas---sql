# 📊 Análise de Despesas das Operadoras de Saúde

![MySQL](https://img.shields.io/badge/MySQL-Database-blue) ![Status](https://img.shields.io/badge/Status-Em%20Desenvolvimento-yellow)

Este repositório contém scripts SQL para criação, carregamento e análise de dados financeiros de operadoras de saúde no Brasil. Os dados incluem despesas com **sinistros/eventos** extraídos de demonstrações contábeis das operadoras registradas na **ANS (Agência Nacional de Saúde Suplementar)**.

---

## 📌 Tabela de Conteúdo
- [Objetivo](#-objetivo)
- [Banco de Dados](#-banco-de-dados)
- [Carregamento dos Dados](#-carregamento-dos-dados)
- [Consultas SQL](#-consultas-sql)
- [Requisitos](#-requisitos)
- [Melhorias Futuras](#-melhorias-futuras)
- [Contribuição](#-contribuição)

---

## 🎯 Objetivo
Este projeto tem como objetivo analisar as despesas das operadoras de saúde com **sinistros conhecidos ou avisados** ao longo do tempo. Isso permite insights sobre os gastos no **último trimestre** e no **último ano**.

---

## 🗄️ Banco de Dados

### **Criação do Banco de Dados**
```sql
CREATE DATABASE dados_operadoras;
```

### **1️⃣ Tabela `operadoras_ativas`**
Contém informações cadastrais das operadoras de saúde registradas na ANS.
```sql
CREATE TABLE operadoras_ativas (
    Registro_ANS VARCHAR(255),
    CNPJ VARCHAR(255),
    Razao_Social VARCHAR(255),
    Nome_Fantasia VARCHAR(255),
    Modalidade VARCHAR(255),
    Logradouro VARCHAR(255),
    Numero VARCHAR(255),
    Complemento VARCHAR(255),
    Bairro VARCHAR(255),
    Cidade VARCHAR(255),
    UF VARCHAR(255),
    CEP VARCHAR(255),
    DDD VARCHAR(255),
    Telefone VARCHAR(255),
    Fax VARCHAR(255),
    Endereco_eletronico VARCHAR(255),
    Representante VARCHAR(255),
    Cargo_Representante VARCHAR(255),
    Regiao_de_Comercializacao VARCHAR(255),
    Data_Registro_ANS DATE
);
```

### **2️⃣ Tabela `demonstracoes_contabeis`**
Armazena informações financeiras das operadoras, incluindo despesas com **sinistros/eventos**.
```sql
CREATE TABLE demonstracoes_contabeis (
    DATA DATE,
    REG_ANS VARCHAR(255),
    CD_CONTA_CONTABIL VARCHAR(255),
    DESCRICAO VARCHAR(255),
    VL_SALDO_INICIAL DECIMAL(18, 2),
    VL_SALDO_FINAL DECIMAL(18, 2)
);
```

---

## 📥 Carregamento dos Dados
Os dados são carregados via `LOAD DATA INFILE` diretamente de arquivos `.csv`.

```sql
LOAD DATA LOCAL INFILE 'C:\\Users\\Lucas\\Desktop\\querys\\Dados Cadastrais Operadoras\\Relatorio_cadop.csv'
INTO TABLE operadoras_ativas
CHARACTER SET utf8
FIELDS TERMINATED BY ';'
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```

```sql
LOAD DATA LOCAL INFILE 'C:\\Users\\Lucas\\Desktop\\querys\\Demonstracoes Contabeis\\2024\\4T2024.csv'
INTO TABLE demonstracoes_contabeis
CHARACTER SET utf8
FIELDS TERMINATED BY ';'
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```

---

## 📊 Consultas SQL

### 🔹 **Top 10 Operadoras com Maiores Despesas em Sinistros no Último Trimestre**
```sql
SELECT
    OA.RAZAO_SOCIAL,
    SUM(COALESCE(DC.VL_SALDO_FINAL, 0)) AS Total_Despesas
FROM
    DEMONSTRACOES_CONTABEIS DC
INNER JOIN
    OPERADORAS_ATIVAS OA
    ON DC.REG_ANS = OA.REGISTRO_ANS
WHERE
    DC.DESCRICAO LIKE '%SINISTROS%'
    AND DC.DATA = (SELECT MAX(DATA) FROM DEMONSTRACOES_CONTABEIS)  -- Último trimestre disponível
GROUP BY
    OA.RAZAO_SOCIAL
ORDER BY
    Total_Despesas DESC
LIMIT 10;
```

### 🔹 **Top 10 Operadoras com Maiores Despesas em Sinistros no Último Ano**
```sql
SELECT
    OA.RAZAO_SOCIAL,
    SUM(COALESCE(DC.VL_SALDO_FINAL, 0)) AS Total_Despesas
FROM
    DEMONSTRACOES_CONTABEIS DC
INNER JOIN
    OPERADORAS_ATIVAS OA
    ON DC.REG_ANS = OA.REGISTRO_ANS
WHERE
    DC.DESCRICAO LIKE '%SINISTROS%'
    AND DC.DATA BETWEEN '2024-01-01' AND (SELECT MAX(DATA) FROM DEMONSTRACOES_CONTABEIS)
GROUP BY
    OA.RAZAO_SOCIAL
ORDER BY
    Total_Despesas DESC
LIMIT 10;
```

---

## 📌 Requisitos
- **Banco de Dados:** MySQL
- **Arquivos CSV:** Devem estar corretamente formatados com separação por `;`
- **Permissão para `LOAD DATA LOCAL INFILE`** no MySQL

---

## ✨ Melhorias Futuras
🔹 Criar visualizações gráficas das despesas por trimestre 📊  
🔹 Comparar as despesas entre diferentes anos 📆  
🔹 Implementar uma API para consultas dinâmicas 💻  

---

## 📩 Contribuição
Se quiser contribuir com melhorias ou sugestões, fique à vontade para abrir uma **issue** ou enviar um **pull request**. 🚀

---

Feito com 💙 por Lucas.



Permissão para LOAD DATA LOCAL INFILE no MySQL
