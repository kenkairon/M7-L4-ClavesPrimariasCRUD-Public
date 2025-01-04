# M7-L4-ClavesPrimariasCRUD-Public
Educativo y de Aprendizaje Personal


---
## Tabla de Contenidos
- [Tecnologías](#Tecnologías)
- [Configuración Inicial](#configuración-Inicial)
- [Creación del Modelo](#creación-del-modelo)
- [Creación de Vistas](#creación-de-vistas)

---
# Tecnologías
- Django: Framework web en Python.
- PostgreSQL: Base de datos relacional avanzada 
--- 
# Configuración Inicial 
1. Entorno virtual 
    ```bash 
    python -m venv venv

2. Activar el entorno virtual
    ```bash 
    venv\Scripts\activate

3. Instalar Django
    ```bash 
    pip install django 

4. Actulizamos el pip 
    ```bash
    python.exe -m pip install --upgrade pip

5. Crear el proyecto de django
    ```bash 
    django-admin startproject crud

6. Ingresamos al proyecto crud
    ```bash 
    cd crud

7. Creamos la aplicacion llamada app1
    ```bash     
    python manage.py startapp app1

8. Creamos la aplicacion llamada app2
    ```bash     
    python manage.py startapp app2

9. Configuración de crud/settings.py 
    ```bash 
    INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app1',
    'app2',
    ]

10. Guardo las dependencias me voy un cd .. mas atras del proyecto principal con el objetivo que quede al lado del README.md
    ```bash
    cd ..
    cd ..
    pip freeze > requirements.txt

# Creación del Modelo 

14. en app1/models.py
    ```bash
    from django.db import models
    from app2.models import Curso

    # Create your models here.
    class Estudiante(models.Model):
        id = models.AutoField(primary_key=True)
        nombre = models.CharField(max_length=100)
        curso = models.ForeignKey(Curso, on_delete=models.CASCADE)

        def __str__(self):
            return f"Nombre: {self.nombre} -curso: {self.curso}"

15. en app2/models.py
    ```bash
    from django.db import models

    # Create your models here.
    class Curso(models.Model):
        id = models.AutoField(primary_key=True)
        nombre = models.CharField(max_length=100)
        detalle = models.TextField()
        def __str__(self):
            return self.nombre

16. Ejecuta las migraciones para aplicar estos cambios a la base de datos:
    ```bash 
    python manage.py makemigrations
    python manage.py migrate

17. gestion_cursos/forms.py
    ```bash 
    from django import forms
    from .models import Estudiante

    class EstudianteForm(forms.ModelForm):
        class Meta:
            model = Estudiante
            fields = '__all__'


## Creación de Vistas
18. app1/views.py 
    ```bash 
    from django.shortcuts import render, get_object_or_404, redirect
    from .models import Estudiante
    from .forms import EstudianteForm

    # Read Estudiante(leer todos los estudiantes)
    def estudiante_list(request):
        estudiantes = Estudiante.objects.all()
        return render(request, 'app1/estudiante_list.html', {'estudiantes': estudiantes})

    # Read Estudiante(leer un estudiante)
    def estudiante_detail(request, pk):
        estudiante = get_object_or_404(Estudiante, pk=pk)
        return render(request, 'app1/estudiante_detail.html', {'estudiante': estudiante})

    # Crear Estudiante
    def estudiante_create(request):
        if request.method == "POST":
            form = EstudianteForm(request.POST)
            if form.is_valid():
                form.save()
                return redirect('estudiante_list')
        else:
            form = EstudianteForm()
        return render(request, 'app1/estudiante_form.html', {'form': form})

    # Actualizar Estudiante
    def estudiante_update(request, pk):
        estudiante = get_object_or_404(Estudiante, pk=pk)
        if request.method == "POST":
            form = EstudianteForm(request.POST, instance=estudiante)
            if form.is_valid():
                form.save()
                return redirect('estudiante_list')
        else:
            form = EstudianteForm(instance=estudiante)
        return render(request, 'app1/estudiante_form.html', {'form': form})

    # Eliminar Estudiante
    def estudiante_delete(request, pk):
        estudiante = get_object_or_404(Estudiante, pk=pk)
        if request.method == "POST":
            estudiante.delete()
            return redirect('estudiante_list')
        return render(request, 'app1/estudiante_delete.html', {'estudiante': estudiante})

20. creamos en templates/app1/estudiante_list.html 
    ```bash 
    <!DOCTYPE html>
    <html>

    <head>
        <title>Estudiantes List</title>
    </head>

    <body>
        <h1>Estudiantes List</h1>
        <a href="{% url 'estudiante_create' %}">Create New</a>
        <ul>
            {% for estudiante in estudiantes %}
            <li>
                <a href="{% url 'estudiante_detail' estudiante.id %}">{{ estudiante.nombre }}</a>
                <a href="{% url 'estudiante_update' estudiante.id %}">Edit</a>
                <a href="{% url 'estudiante_delete' estudiante.id %}">Delete</a>
            </li>
            {% endfor %}
        </ul>
    </body>

    </html>
21. creamos en templates/app1/detail.html
    ```bash 
    <!DOCTYPE html>
    <html>

    <head>
        <title>Estudiante Detail</title>
    </head>

    <body>
        <h1>{{ estudiante.nombre }}</h1>
        <p>Curso: {{ estudiante.curso.nombre }}</p>
        <a href="{% url 'estudiante_update' estudiante.id %}">Edit</a>
        <a href="{% url 'estudiante_list' %}">Back to List</a>
    </body>

    </html>

22. creamos en templates/app1/estudiante_form.html

    ```bash 
    <!DOCTYPE html>
    <html>

    <head>
        <title>Estudiante Form</title>
    </head>

    <body>
        <h1>Estudiante Form</h1>
        <form method="post">
            {% csrf_token %}
            {{ form.as_p }}
            <button type="submit">Save</button>
        </form>
    </body>

    </html>

23. creamos en templates/app1/estudiante_delete.html
    ```bash 
    <!DOCTYPE html>
    <html>

    <head>
        <title>Delete Estudiante</title>
    </head>

    <body>
        <h1>Delete Estudiante</h1>
        <p>Are you sure you want to delete "{{ estudiante.nombre }}"?</p>
        <form method="post">
            {% csrf_token %}
            <button type="submit">Yes, delete</button>
        </form>
        <a href="{% url 'estudiante_list' %}">Cancel</a>
    </body>

    </html>

24. Activamos el Servidor http://127.0.0.1:8000/
    ```bash 
    python manage.py runserver

