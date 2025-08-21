# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: __8__ syscalls
- ex1b_write: __7__ syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
Há uma diferenca de um syscall, ja que o como o printf precisa dar outro syscall adicional para acionar o sistema e mudar o arquivo de uma so vez, ja o write escreve no arquivo todas vez, então nao ha necessidade de uma ultima syscall adicional
```

**3. Qual método é mais previsível? Por quê você acha isso?**
write, uma vez que cada syscall corresponde a uma mudança no arquivo então o numero de syscalls equivale ao numero de escritas
```
[Sua análise aqui]
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: __3__
- Bytes lidos: __9__

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
foi usado o 3, não começou em 0, 1 ou 2 pois estes ja estao reservados pelo sistema (STDIN, STDOUT, STDERR)
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
quando o numero de bytes lidos for menor que o do buffer
```

**3. Por que verificar retorno de cada syscall?**

```
para verificar se ocorreu algum erro
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: _____ (esperado: 25)
- Caracteres: _____
- Chamadas read(): _____
- Tempo: _____ segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |        90        |      0.001248     |
| 64          |         24       |       0.000420    |
| 256         |           1      |        0.000747   |
| 1024        |           5      |         0.00000  |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
quanto maior o buffer menor o numero de syscalls
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
Não, a ultima retorna menos 
```

**3. Qual é a relação entre syscalls e performance?**

```
Quanto menos syscall mais rapido
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: __1364__
- Operações: __6__
- Tempo: __0.000452__ segundos
- Throughput: __2946.97__ KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [x] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
para saber se houve uma discrepancia de memoria entre leitura e escrita
```

**2. Que flags são essenciais no open() do destino?**

```
O_CREAT para criar o arquivo
```

**3. O número de reads e writes é igual? Por quê?**

```
sim, ja que o programa utiliza o mesmo buffer do mesmo tamanho para leitura e escrita
```

**4. Como você saberia se o disco ficou cheio?**

```
se a função de escrita retorna-se um numero negativo
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
o sistema mantem o fd do arquivo, possivelmente causando memory leaks
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
syscalls possibilitam que usuarios utilizem funcões que somente o kernel consegue, como entrada e saida de texto
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
são importantes para indentificar os diferentes arquivos abertos
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
quanto maior o tamanho do buffer, menos syscalls sao necessarias, então mais performance
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** __o programa__

**Por que você acha que foi mais rápido?**

```
Acredito que tenha sido mais rapido ja que cp possui outras funcionalidades, ja que o programa feito por nós é bem mais leve e ja foi feito para ler e copiar este arquivo em especifico
```

---

## 📤 Entrega
Certifique-se de ter:
- [x] Todos os códigos com TODOs completados
- [x] Traces salvos em `traces/`
- [x] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
