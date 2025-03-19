import sqlite3
import tkinter as tk
from tkinter import messagebox, ttk

def conectar_bd():
    return sqlite3.connect("inventario.db")

def inicializar_bd():
    with conectar_bd() as con:
        cur = con.cursor()
        cur.execute('''CREATE TABLE IF NOT EXISTS productos (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        nombre TEXT NOT NULL,
                        cantidad INTEGER NOT NULL,
                        precio REAL NOT NULL,
                        fecha_compra TEXT,
                        fecha_venta TEXT
                    )''')
        con.commit()

def agregar_producto():
    nombre = entry_nombre.get()
    cantidad = entry_cantidad.get()
    precio = entry_precio.get()
    fecha_compra = entry_fecha_compra.get()
    
    if not nombre or not cantidad.isdigit() or not precio.replace('.', '', 1).isdigit():
        messagebox.showerror("Error", "Por favor ingrese datos válidos.")
        return
    
    with conectar_bd() as con:
        cur = con.cursor()
        cur.execute("INSERT INTO productos (nombre, cantidad, precio, fecha_compra) VALUES (?, ?, ?, ?)",
                    (nombre, int(cantidad), float(precio), fecha_compra))
        con.commit()
    actualizar_tabla()

def actualizar_tabla():
    for row in tabla.get_children():
        tabla.delete(row)
    with conectar_bd() as con:
        cur = con.cursor()
        cur.execute("SELECT * FROM productos")
        for row in cur.fetchall():
            tabla.insert("", "end", values=row)

def calcular_iva():
    seleccionado = tabla.focus()
    if not seleccionado:
        messagebox.showwarning("Advertencia", "Seleccione un producto.")
        return
    valores = tabla.item(seleccionado, "values")
    precio = float(valores[3])
    iva = precio * 0.16
    messagebox.showinfo("Cálculo de IVA", f"El IVA del producto '{valores[1]}' es: ${iva:.2f}")

def verificar_stock():
    with conectar_bd() as con:
        cur = con.cursor()
        cur.execute("SELECT nombre FROM productos WHERE cantidad <= 0")
        agotados = cur.fetchall()
        if agotados:
            productos_agotados = ", ".join(p[0] for p in agotados)
            messagebox.showwarning("Stock Agotado", f"Productos agotados: {productos_agotados}")
        else:
            messagebox.showinfo("Stock", "No hay productos agotados.")

# Interfaz Gráfica con Tkinter
root = tk.Tk()
root.title("Inventario de Ferretería")
root.geometry("600x400")

frame = tk.Frame(root)
frame.pack(pady=10)

# Entradas
entry_nombre = tk.Entry(frame, width=20)
entry_nombre.grid(row=0, column=1)
entry_cantidad = tk.Entry(frame, width=10)
entry_cantidad.grid(row=0, column=3)
entry_precio = tk.Entry(frame, width=10)
entry_precio.grid(row=1, column=1)
entry_fecha_compra = tk.Entry(frame, width=15)
entry_fecha_compra.grid(row=1, column=3)

# Etiquetas
tk.Label(frame, text="Nombre").grid(row=0, column=0)
tk.Label(frame, text="Cantidad").grid(row=0, column=2)
tk.Label(frame, text="Precio").grid(row=1, column=0)
tk.Label(frame, text="Fecha Compra").grid(row=1, column=2)

# Botones
tk.Button(frame, text="Agregar Producto", command=agregar_producto).grid(row=2, column=0, columnspan=4, pady=5)
tk.Button(frame, text="Calcular IVA", command=calcular_iva).grid(row=3, column=0, columnspan=2, pady=5)
tk.Button(frame, text="Verificar Stock", command=verificar_stock).grid(row=3, column=2, columnspan=2, pady=5)

# Tabla
columns = ("ID", "Nombre", "Cantidad", "Precio", "Fecha Compra", "Fecha Venta")
tabla = ttk.Treeview(root, columns=columns, show="headings")
for col in columns:
    tabla.heading(col, text=col)
tabla.pack(fill=tk.BOTH, expand=True)

inicializar_bd()
actualizar_tabla()
root.mainloop()


