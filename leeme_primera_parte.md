


## Proyecto: Taller de Costura

**Lenguaje:** Python  

**Framework:** Django  

**Editor:** VS Code



---



### 🧱 models.py

```python

from django.db import models



# ==========================================

# MODELO: ALUMNO

# ==========================================

class Alumno(models.Model):

    nombre = models.CharField(max_length=100)

    apellido = models.CharField(max_length=100)

    correo = models.EmailField(unique=True)

    telefono = models.CharField(max_length=15)

    direccion = models.CharField(max_length=200)

    fecha_nacimiento = models.DateField()

    fecha_registro = models.DateField(auto_now_add=True)



    clases = models.ManyToManyField('Clase', through='Inscripcion', related_name='alumnos')



    def __str__(self):

        return f"{self.nombre} {self.apellido}"





# ==========================================

# MODELO: CLASE

# ==========================================

class Clase(models.Model):

    nombre = models.CharField(max_length=100)

    descripcion = models.TextField()

    nivel = models.CharField(max_length=50, choices=[

        ('Básico', 'Básico'),

        ('Intermedio', 'Intermedio'),

        ('Avanzado', 'Avanzado'),

    ])

    fecha_inicio = models.DateField()

    fecha_fin = models.DateField()

    duracion_horas = models.PositiveIntegerField()

    cupo_maximo = models.PositiveIntegerField()



    def __str__(self):

        return f"{self.nombre} ({self.nivel})"





# ==========================================

# MODELO: INSCRIPCION

# ==========================================

class Inscripcion(models.Model):

    alumno = models.ForeignKey(Alumno, on_delete=models.CASCADE)

    clase = models.ForeignKey(Clase, on_delete=models.CASCADE)

    fecha_inscripcion = models.DateField(auto_now_add=True)

    metodo_pago = models.CharField(max_length=50, choices=[

        ('Efectivo', 'Efectivo'),

        ('Tarjeta', 'Tarjeta'),

        ('Transferencia', 'Transferencia'),

    ])

    monto_pagado = models.DecimalField(max_digits=8, decimal_places=2)

    estado = models.CharField(max_length=50, choices=[

        ('Activa', 'Activa'),

        ('Cancelada', 'Cancelada'),

        ('Finalizada', 'Finalizada'),

    ])

    observaciones = models.TextField(blank=True, null=True)



    def __str__(self):

        return f"{self.alumno} → {self.clase}"

```



---



### ⚙️ views.py (Funciones CRUD para Alumnos)

```python

from django.shortcuts import render, redirect, get_object_or_404

from .models import Alumno



def inicio_tallercostura(request):

    return render(request, 'inicio.html')



def agregar_alumno(request):

    if request.method == 'POST':

        nombre = request.POST['nombre']

        apellido = request.POST['apellido']

        correo = request.POST['correo']

        telefono = request.POST['telefono']

        direccion = request.POST['direccion']

        fecha_nacimiento = request.POST['fecha_nacimiento']

        Alumno.objects.create(

            nombre=nombre,

            apellido=apellido,

            correo=correo,

            telefono=telefono,

            direccion=direccion,

            fecha_nacimiento=fecha_nacimiento

        )

        return redirect('ver_alumno')

    return render(request, 'alumno/agregar_alumno.html')



def ver_alumno(request):

    alumnos = Alumno.objects.all()

    return render(request, 'alumno/ver_alumno.html', {'alumnos': alumnos})



def actualizar_alumno(request, id):

    alumno = get_object_or_404(Alumno, id=id)

    return render(request, 'alumno/actualizar_alumno.html', {'alumno': alumno})



def realizar_actualizacion_alumno(request, id):

    alumno = get_object_or_404(Alumno, id=id)

    if request.method == 'POST':

        alumno.nombre = request.POST['nombre']

        alumno.apellido = request.POST['apellido']

        alumno.correo = request.POST['correo']

        alumno.telefono = request.POST['telefono']

        alumno.direccion = request.POST['direccion']

        alumno.fecha_nacimiento = request.POST['fecha_nacimiento']

        alumno.save()

        return redirect('ver_alumno')

    return render(request, 'alumno/actualizar_alumno.html', {'alumno': alumno})



def borrar_alumno(request, id):

    alumno = get_object_or_404(Alumno, id=id)

    if request.method == 'POST':

        alumno.delete()

        return redirect('ver_alumno')

    return render(request, 'alumno/borrar_alumno.html', {'alumno': alumno})

```



---



### 🌐 urls.py (app_taller)

```python

from django.urls import path

from . import views



urlpatterns = [

    path('', views.inicio_tallercostura, name='inicio_tallercostura'),

    path('alumnos/', views.ver_alumno, name='ver_alumno'),

    path('alumnos/agregar/', views.agregar_alumno, name='agregar_alumno'),

    path('alumnos/actualizar/<int:id>/', views.actualizar_alumno, name='actualizar_alumno'),

    path('alumnos/realizar_actualizacion/<int:id>/', views.realizar_actualizacion_alumno, name='realizar_actualizacion_alumno'),

    path('alumnos/borrar/<int:id>/', views.borrar_alumno, name='borrar_alumno'),

]

```



---



### 🌍 urls.py (backend_tallercostura)

```python

from django.contrib import admin

from django.urls import path, include



urlpatterns = [

    path('admin/', admin.site.urls),

    path('', include('app_taller.urls')),

]

```



---



### 🧩 admin.py

```python

from django.contrib import admin

from .models import Alumno, Clase, Inscripcion



admin.site.register(Alumno)

admin.site.register(Clase)

admin.site.register(Inscripcion)

```



---



### 🎨 base.html

```html

<!DOCTYPE html>

<html lang="es">

<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Sistema Taller de Costura</title>

    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

</head>

<body class="bg-light">

    {% include 'header.html' %}

    {% include 'navbar.html' %}

    <main class="container mt-4">

        {% block content %}{% endblock %}

    </main>

    {% include 'footer.html' %}

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

</body>

</html>

```



---



### 🧭 navbar.html

```html

<nav class="navbar navbar-expand-lg navbar-dark bg-primary">

    <div class="container-fluid">

        <a class="navbar-brand" href="#">🧵 Sistema de Administración Taller de Costura</a>

        <div class="collapse navbar-collapse">

            <ul class="navbar-nav ms-auto">

                <li class="nav-item"><a class="nav-link" href="/">Inicio</a></li>

                <li class="nav-item dropdown">

                    <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">Alumnos</a>

                    <ul class="dropdown-menu">

                        <li><a class="dropdown-item" href="/alumnos/agregar/">Agregar alumno</a></li>

                        <li><a class="dropdown-item" href="/alumnos/">Ver alumnos</a></li>

                    </ul>

                </li>

            </ul>

        </div>

    </div>

</nav>

```



---



### 🪶 footer.html

```html

<footer class="bg-dark text-white text-center py-3 mt-5 fixed-bottom">

    <p>© {{ fecha_actual }} Creado por técnico Emma Ortiz, CBTIS 128</p>

</footer>

```



---



### 🏠 inicio.html

```html

{% extends 'base.html' %}

{% block content %}

<div class="text-center">

    <h1>Bienvenido al Sistema Taller de Costura</h1>

    <img src="https://cdn.pixabay.com/photo/2017/01/31/19/14/sewing-2029599_1280.jpg" class="img-fluid mt-3" alt="Taller de costura">

</div>

{% endblock %}

```



---



### 🧍‍♀️ agregar_alumno.html

```html

{% extends 'base.html' %}

{% block content %}

<h2>Agregar Alumno</h2>

<form method="POST">

    {% csrf_token %}

    <input type="text" name="nombre" placeholder="Nombre" class="form-control mb-2">

    <input type="text" name="apellido" placeholder="Apellido" class="form-control mb-2">

    <input type="email" name="correo" placeholder="Correo" class="form-control mb-2">

    <input type="text" name="telefono" placeholder="Teléfono" class="form-control mb-2">

    <input type="text" name="direccion" placeholder="Dirección" class="form-control mb-2">

    <input type="date" name="fecha_nacimiento" class="form-control mb-2">

    <button type="submit" class="btn btn-success">Guardar</button>

</form>

{% endblock %}

```



---



### 📋 ver_alumno.html

```html

{% extends 'base.html' %}

{% block content %}

<h2>Lista de Alumnos</h2>

<table class="table table-bordered">

    <thead>

        <tr>

            <th>Nombre</th>

            <th>Apellido</th>

            <th>Correo</th>

            <th>Teléfono</th>

            <th>Acciones</th>

        </tr>

    </thead>

    <tbody>

        {% for alumno in alumnos %}

        <tr>

            <td>{{ alumno.nombre }}</td>

            <td>{{ alumno.apellido }}</td>

            <td>{{ alumno.correo }}</td>

            <td>{{ alumno.telefono }}</td>

            <td>

                <a href="{% url 'actualizar_alumno' alumno.id %}" class="btn btn-warning btn-sm">Editar</a>

                <a href="{% url 'borrar_alumno' alumno.id %}" class="btn btn-danger btn-sm">Borrar</a>

            </td>

        </tr>

        {% endfor %}

    </tbody>

</table>

{% endblock %}

```



---



### ✏️ actualizar_alumno.html

```html

{% extends 'base.html' %}

{% block content %}

<h2>Actualizar Alumno</h2>

<form method="POST">

    {% csrf_token %}

    <input type="text" name="nombre" value="{{ alumno.nombre }}" class="form-control mb-2">

    <input type="text" name="apellido" value="{{ alumno.apellido }}" class="form-control mb-2">

    <input type="email" name="correo" value="{{ alumno.correo }}" class="form-control mb-2">

    <input type="text" name="telefono" value="{{ alumno.telefono }}" class="form-control mb-2">

    <input type="text" name="direccion" value="{{ alumno.direccion }}" class="form-control mb-2">

    <input type="date" name="fecha_nacimiento" value="{{ alumno.fecha_nacimiento }}" class="form-control mb-2">

    <button type="submit" class="btn btn-primary">Actualizar</button>

</form>

{% endblock %}

```



---



### ❌ borrar_alumno.html

```html

{% extends 'base.html' %}

{% block content %}

<h2>¿Seguro que deseas borrar este alumno?</h2>

<p>{{ alumno.nombre }} {{ alumno.apellido }}</p>

<form method="POST">

    {% csrf_token %}

    <button type="submit" class="btn btn-danger">Eliminar</button>

    <a href="{% url 'ver_alumno' %}" class="btn btn-secondary">Cancelar</a>

</form>

{% endblock %}

```
##ESTRUCTURA DE PROYECTO##
```
UIII_taller_costura_0306/
├─ .venv/                         # Entorno virtual de Python
│
├─ manage.py                      # Archivo principal para ejecutar comandos Django
│
├─ backend_tallercostura/         # Carpeta del proyecto Django (backend)
│  ├─ __init__.py
│  ├─ asgi.py
│  ├─ settings.py                 # Configuración principal del proyecto
│  ├─ urls.py                     # Enrutador principal (enlaza con las apps)
│  ├─ wsgi.py
│
├─ app_taller/                    # Aplicación principal del proyecto
│  ├─ __init__.py
│  ├─ admin.py                    # Registro de modelos en el panel de administración
│  ├─ apps.py
│  ├─ models.py                   # Modelos: Alumno, Clase, Inscripción
│  ├─ views.py                    # Funciones CRUD (solo Alumno por ahora)
│  ├─ urls.py                     # Rutas internas de la app_taller
│  ├─ tests.py
│  ├─ migrations/                 # Archivos automáticos de base de datos
│  │  └─ __init__.py
│  │
│  ├─ templates/                  # Carpeta de plantillas HTML
│  │  ├─ base.html                # Plantilla base con Bootstrap
│  │  ├─ header.html              # Cabecera (si se usa)
│  │  ├─ navbar.html              # Menú de navegación con íconos
│  │  ├─ footer.html              # Pie de página con autor y fecha
│  │  ├─ inicio.html              # Página principal del sistema
│  │  │
│  │  └─ alumno/                  # Subcarpeta con vistas de alumnos
│  │      ├─ agregar_alumno.html
│  │      ├─ ver_alumno.html
│  │      ├─ actualizar_alumno.html
│  │      └─ borrar_alumno.html
│  │
│  └─ static/                     # (Opcional) Carpeta para archivos estáticos: CSS, JS, imágenes
│      ├─ css/
│      ├─ js/
│      └─ img/
│
└─ db.sqlite3                     # Base de datos SQLite (se crea después de migrar)

```



---



🧵 **Fin del documento - Proyecto Taller de Costura (Emma Ortiz)**

"""



