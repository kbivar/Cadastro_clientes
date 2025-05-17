# Cadastro_clientes_motoshop🚀

sistema utilizado para cadastar e consultar clientes ja cadastrados, garantindo que quem for cadastrado receba 10% de desconto.

# 🔌Como fazer funcionar na sua máquina:

- Instale Python na sua máquina a partir da versa 3.13;
- Instale a biblioteca TKINTER e import sqlite3 # banco de dados
.

# 📋Pré-requisitos do sistema:

> Todos os dados devem estar preenchidos para um cadastro ser realizado


## 🛠️Tecnologias utilizadas:

> Editor de código
Linguagens
Bibliotecas

## Versões:

> Python 3.13
> 

## Autores:

> Kleber Bivar




import sqlite3
from tkinter import Tk, Label, Entry, Button, Listbox, PhotoImage, END, messagebox

# Conectar ao banco de dados SQLite
conn = sqlite3.connect("clientes.db")
cursor = conn.cursor()

# Criar a tabela se não existir
cursor.execute("""
CREATE TABLE IF NOT EXISTS clientes (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT NOT NULL,
    telefone TEXT NOT NULL,
    endereco TEXT NOT NULL,
    placa TEXT NOT NULL UNIQUE
)
""")
conn.commit()

# Função para cadastrar um cliente
def cadastrar_cliente():
    nome = entry_nome.get()
    telefone = entry_telefone.get()
    endereco = entry_endereco.get()
    placa = entry_placa.get()

    if not nome or not telefone or not endereco or not placa:
        messagebox.showwarning("Campos obrigatórios", "Todos os campos devem ser preenchidos!")
        return

    # Verificar se a placa já está cadastrada
    cursor.execute("SELECT * FROM clientes WHERE placa = ?", (placa,))
    cliente_existente = cursor.fetchone()

    if cliente_existente:
        messagebox.showinfo("Placa já cadastrada", f"A placa {placa} já está registrada.\n"
                                                   "O cliente tem direito a 10% de desconto!")
        return

    # Inserir cliente no banco de dados
    cursor.execute("""
    INSERT INTO clientes (nome, telefone, endereco, placa)
    VALUES (?, ?, ?, ?)
    """, (nome, telefone, endereco, placa))
    conn.commit()

    messagebox.showinfo("Sucesso", "Cliente cadastrado com sucesso!")
    entry_nome.delete(0, END)
    entry_telefone.delete(0, END)
    entry_endereco.delete(0, END)
    entry_placa.delete(0, END)
    listar_clientes()

# Função para listar os clientes cadastrados
def listar_clientes():
    listbox_clientes.delete(0, END)  # Limpar a lista
    cursor.execute("SELECT * FROM clientes")
    clientes = cursor.fetchall()

    for cliente in clientes:
        listbox_clientes.insert(END, f"ID: {cliente[0]} | Nome: {cliente[1]} | Telefone: {cliente[2]} | "
                                     f"Endereço: {cliente[3]} | Placa: {cliente[4]}")

# Função para deletar um cliente
def deletar_cliente():
    selecionado = listbox_clientes.curselection()
    if not selecionado:
        messagebox.showwarning("Seleção necessária", "Por favor, selecione um cliente para deletar!")
        return

    cliente = listbox_clientes.get(selecionado)
    cliente_id = cliente.split(" | ")[0].split(": ")[1]  # Extrair o ID do cliente

    cursor.execute("DELETE FROM clientes WHERE id = ?", (cliente_id,))
    conn.commit()

    messagebox.showinfo("Sucesso", "Cliente deletado com sucesso!")
    listar_clientes()

# Interface Tkinter
janela = Tk()
janela.title("Cadastro de Clientes - Loja de Peças de Moto")
janela.geometry("800x500")

# Carregar imagem
imagem_path = "ttt.png"  # Substitua pelo caminho da sua imagem
try:
    imagem = PhotoImage(file=imagem_path)
except:
    imagem = None  # Caso a imagem não seja encontrada

if imagem:
    Label(janela, image=imagem).grid(row=0, column=1, rowspan=1, padx=10, pady=2)

# Campos para cadastro
Label(janela, text="Nome:").grid(row=0, column=1, padx=10, pady=10, sticky="e")
entry_nome = Entry(janela, width=30)
entry_nome.grid(row=0, column=2, padx=10, pady=10)

Label(janela, text="Telefone:").grid(row=1, column=1, padx=10, pady=10, sticky="e")
entry_telefone = Entry(janela, width=30)
entry_telefone.grid(row=1, column=2, padx=10, pady=10)

Label(janela, text="Endereço:").grid(row=2, column=1, padx=10, pady=10, sticky="e")
entry_endereco = Entry(janela, width=30)
entry_endereco.grid(row=2, column=2, padx=10, pady=10)

Label(janela, text="Placa:").grid(row=3, column=1, padx=10, pady=10, sticky="e")
entry_placa = Entry(janela, width=30)
entry_placa.grid(row=3, column=2, padx=10, pady=10)

# Botão de cadastrar
btn_cadastrar = Button(janela, text="Cadastrar Cliente", command=cadastrar_cliente, bg="lightblue")
btn_cadastrar.grid(row=4, column=1, columnspan=2, pady=20)

# Lista de clientes
Label(janela, text="Clientes cadastrados:").grid(row=5, column=1, columnspan=2)
listbox_clientes = Listbox(janela, width=90, height=10)
listbox_clientes.grid(row=6, column=1, columnspan=2, padx=10, pady=10)

# Botão de atualizar lista
btn_listar = Button(janela, text="Atualizar Lista", command=listar_clientes, bg="lightgreen")
btn_listar.grid(row=7, column=1, pady=10)

# Botão de deletar cliente
btn_deletar = Button(janela, text="Deletar Cliente", command=deletar_cliente, bg="lightcoral")
btn_deletar.grid(row=7, column=2, pady=10)

# Inicializar a listagem ao abrir o programa
listar_clientes()

# Iniciar o loop da interface
janela.mainloop()

# Fechar conexão com o banco de dados ao encerrar o programa
conn.close()
