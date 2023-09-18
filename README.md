# Separador_por_Guias_y_bultos
Código en python para unir diferentes pdf por código y ordenarlo por bulto para el cliente ARUMA
#Código para Separador por ASN - PARA ARUMA

#//PARA MODIFICAR COMUNICARSE CON:
    
#//GIAN CARLOS CARRIL - CD - CELENDÓN
#//CORRREO CORPORATIVO:PRACTICANTE.CELENDON@UNIBELL.COM.PE
#//COREO PERSONAL: GIAN_733@HOTMAIL.COM
#//CELULAR: 936988678

import os
from PyPDF2 import PdfMerger

# Nombre del archivo final combinado
nombre_archivo_final = "tu_archivo.pdf"

# Directorio donde se encuentran los archivos PDF
directorio_pdf = "Consolidado"

# Obtener la lista de archivos PDF en el directorio
archivos_pdf = [f for f in os.listdir(directorio_pdf) if f.endswith('.pdf')]

# Crear un objeto PdfMerger
combinador_pdf = PdfMerger()

# Combinar los archivos PDF
for archivo_pdf in archivos_pdf:
    ruta_pdf = os.path.join(directorio_pdf, archivo_pdf)
    combinador_pdf.append(ruta_pdf)

# Guardar el archivo combinado
combinador_pdf.write(nombre_archivo_final)
combinador_pdf.close()

print(f"Se han combinado {len(archivos_pdf)} archivos PDF en '{nombre_archivo_final}'.")


#Código para Separador por ASN - PARA ARUMA

#//PARA MODIFICAR COMUNICARSE CON:
    
#//GIAN CARLOS CARRIL - CD - CELENDÓN
#//CORRREO CORPORATIVO:PRACTICANTE.CELENDON@UNIBELL.COM.PE
#//COREO PERSONAL: GIAN_733@HOTMAIL.COM
#//CELULAR: 936988678
import PyPDF2
import re
import os

# Expresión regular para buscar "ASN" seguido de 8 caracteres
patron = r'ASN.{8}'

# Nombre de la carpeta donde se guardarán los archivos finales
carpeta_final = "Finalizado"

# Crea la carpeta si no existe
if not os.path.exists(carpeta_final):
    os.mkdir(carpeta_final)

# Abre el archivo PDF
with open('tu_archivo.pdf', 'rb') as pdf_file:
    pdf_reader = PyPDF2.PdfReader(pdf_file)
    
    # Inicializa un diccionario para almacenar las páginas en las que se encuentra cada código
    paginas_con_codigos = {}
    
    # Itera a través de cada página del PDF
    for page_number, page in enumerate(pdf_reader.pages, 1):
        # Extrae el texto de la página
        page_text = page.extract_text()
        
        # Busca todas las coincidencias del patrón en la página actual
        coincidencias = re.findall(patron, page_text, re.IGNORECASE)
        
        # Si se encontraron coincidencias, agrega la página actual al diccionario
        if coincidencias:
            # Creación de clave única para cada conjunto de coincidencias
            clave = f"Código {'-'.join(coincidencias)}"
            if clave not in paginas_con_codigos:
                paginas_con_codigos[clave] = []
            paginas_con_codigos[clave].append(page_number)

    # Imprime las páginas en las que se encontraron los códigos y los propios códigos
    for clave, paginas in paginas_con_codigos.items():
        print(f"Códigos encontrados en páginas {', '.join(map(str, paginas))}:")
        print(f"  - Códigos: {clave}")

    # Empieza a leer el pdf denuevo desde el principio 
    pdf_file.seek(0)

    # Creando el diccionario para mantener un registro de los PDFs unidos
    pdfs_unidos = {}

    # Itera a través de cada página del PDF
    for page_number, page in enumerate(pdf_reader.pages):
        # Crea una clave única para cada conjunto de coincidencias en la página actual
        page_text = page.extract_text()
        coincidencias = re.findall(patron, page_text, re.IGNORECASE)
        clave = f"Código {'-'.join(coincidencias)}"

        # Si la clave ya está en el diccionario de PDFs unidos, agrega la página actual
        if clave in pdfs_unidos:
            pdfs_unidos[clave].append(page)
        else:
            # Si es la primera vez que se encuentra esta clave, crea una nueva lista de páginas
            pdfs_unidos[clave] = [page]

    # parte para crear un archivo PDF para cada conjunto de páginas unidas y guárdalos en la carpeta "Finalizado"
    for clave, pages in pdfs_unidos.items():
        pdf_writer = PyPDF2.PdfWriter()
        for page in pages:
            pdf_writer.add_page(page)

        # parte para generar un nombre de archivo para el PDF unido en la carpeta "Finalizado"
        output_filename = os.path.join(carpeta_final, f'codigo_{clave}.pdf')

        # Guarda el PDF unido en un archivo
        with open(output_filename, 'wb') as output_file:
            pdf_writer.write(output_file)

    print(f"Unión de PDFs completada. Se crearon {len(pdfs_unidos)} archivos PDF unidos por código en la carpeta 'Finalizado'.")

#//PARA MODIFICAR COMUNICARSE CON:
    
#//GIAN CARLOS CARRIL - CD - CELENDÓN
#//CORREO CORPORATIVO:PRACTICANTE.CELENDON@UNIBELL.COM.PE
#//CORREO PERSONAL: GIAN_733@HOTMAIL.COM
#//CELULAR: 936988678

print("GIAN CARLOS CARRIL - CD - CELENDÓN")





import os
import glob
from PyPDF2 import PdfReader

# Directorio donde se encuentran los archivos PDF
carpeta_finalizado = "Finalizado"

# Función para buscar el patrón y extraer los 3 caracteres a la derecha
def buscar_patron(pdf_path):
    pdf_reader = PdfReader(pdf_path)
    codigos = []
    for page_num in range(len(pdf_reader.pages)):
        text = pdf_reader.pages[page_num].extract_text()
        # Busca el patrón "NÚM. BULTO:"
        index = text.find("NÚM. BULTO:")
        while index != -1:
            # Extrae los 3 caracteres a la derecha del patrón
            codigo = text[index + 11:index + 15]
            codigos.append(codigo)
            # Busca el próximo patrón
            index = text.find("NÚM. BULTO:", index + 1)
    return codigos

# Busca en todos los archivos PDF en la carpeta "Finalizado"
pdf_files = glob.glob(os.path.join(carpeta_finalizado, "*.pdf"))

for pdf_file in pdf_files:
    codigos = buscar_patron(pdf_file)
    if codigos:
        for codigo in codigos:
            print(f"Código: {codigo} - PDF: {pdf_file}")
    else:
        print(f"No se encontró el patrón en el PDF: {pdf_file}")







import os
import glob
from PyPDF2 import PdfReader, PdfWriter

# Directorio donde se encuentran los archivos PDF
carpeta_finalizado = "Finalizado"

# Función para buscar el patrón y extraer los 3 caracteres a la derecha
def buscar_patron(pdf_path):
    pdf_reader = PdfReader(pdf_path)
    codigos = []
    for page_num in range(len(pdf_reader.pages)):
        text = pdf_reader.pages[page_num].extract_text()
        # Busca el patrón "NÚM. BULTO:"
        index = text.find("NÚM. BULTO:")
        while index != -1:
            # Extrae los 3 caracteres a la derecha del patrón
            codigo = text[index + 11:index + 15]
            codigos.append((codigo, pdf_reader.pages[page_num]))
            # Busca el próximo patrón
            index = text.find("NÚM. BULTO:", index + 1)
    return codigos

# Función para ordenar las páginas por código
def ordenar_paginas(codigos):
    # Ordena las páginas según los códigos
    codigos_ordenados = sorted(codigos, key=lambda x: int(x[0]) if x[0].strip().isdigit() else float('inf'))
    return [pagina for codigo, pagina in codigos_ordenados]

# Función para generar un nuevo PDF con las páginas ordenadas
def generar_pdf_ordenado(pdf_path, paginas_ordenadas):
    pdf_writer = PdfWriter()
    
    # Agrega las páginas ordenadas al escritor
    for pagina in paginas_ordenadas:
        pdf_writer.add_page(pagina)

    # Guarda el PDF ordenado
    with open(pdf_path, 'wb') as output_pdf:
        pdf_writer.write(output_pdf)

# Busca en todos los archivos PDF en la carpeta "Finalizado"
pdf_files = glob.glob(os.path.join(carpeta_finalizado, "*.pdf"))

for pdf_file in pdf_files:
    codigos = buscar_patron(pdf_file)
    if codigos:
        paginas_ordenadas = ordenar_paginas(codigos)
        generar_pdf_ordenado(pdf_file, paginas_ordenadas)
        print(f"Se ha reorganizado el PDF: {pdf_file}")
    else:
        print(f"No se encontró el patrón en el PDF: {pdf_file}")








import os
from PyPDF2 import PdfMerger

# Nombre del archivo final combinado
nombre_archivo_final = "Finalito.pdf"

# Directorio donde se encuentran los archivos PDF
directorio_pdf = "Finalizado"

# Obtener la lista de archivos PDF en el directorio
archivos_pdf = [f for f in os.listdir(directorio_pdf) if f.endswith('.pdf')]

# Crear un objeto PdfMerger
combinador_pdf = PdfMerger()

# Combinar los archivos PDF
for archivo_pdf in archivos_pdf:
    ruta_pdf = os.path.join(directorio_pdf, archivo_pdf)
    combinador_pdf.append(ruta_pdf)

# Guardar el archivo combinado
combinador_pdf.write(nombre_archivo_final)
combinador_pdf.close()

print(f"Se han combinado {len(archivos_pdf)} archivos PDF en '{nombre_archivo_final}'.")

#Código para Separador por ASN - PARA ARUMA

#//PARA MODIFICAR COMUNICARSE CON:
    
#//GIAN CARLOS CARRIL - CD - CELENDÓN
#//CORRREO CORPORATIVO:PRACTICANTE.CELENDON@UNIBELL.COM.PE
#//COREO PERSONAL: GIAN_733@HOTMAIL.COM
#//CELULAR: 936988678
print("GIAN CARLOS CARRIL - CD - CELENDÓN")
