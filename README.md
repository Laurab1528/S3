# Guía para Desplegar un Sitio Web Estático en Amazon S3 con GitHub Actions

Esta guía proporciona los pasos necesarios para crear un bucket en Amazon S3, configurar un repositorio en GitHub y utilizar GitHub Actions para desplegar un sitio web estático en el bucket de S3.

## 1. Crear un Bucket de S3 para Hosting de Sitios Web Estáticos

1. **Inicia sesión en la Consola de AWS**:
   - Accede a [AWS Management Console](https://aws.amazon.com/console/) e inicia sesión con tus credenciales.

2. **Navega a Amazon S3**:
   - Busca y selecciona **S3** en la consola de AWS.

3. **Crear un Nuevo Bucket**:
   - Haz clic en **Create bucket**.
   - **Nombre del bucket**: epam-course1528
   - **Región**: us-east-2
   - Haz clic en **Create bucket**.
     ![image](https://github.com/user-attachments/assets/d3cca523-53f7-494c-899a-d77a0202b942)


4. **Configurar el Bucket para Hosting de Sitios Web Estáticos**:
   - Selecciona tu bucket en la lista.
   - Ve a la pestaña **Properties**.
   - En la sección **Static website hosting**, haz clic en **Edit**.
   - Habilita la opción **Enable**.
   - **Index document**: Introduce `index.html`.
   - Haz clic en **Save changes**.

5. **Configurar los Permisos del Bucket**:
   - Ve a la pestaña **Permissions**.
   - En **Bucket Policy**, añade la siguiente política JSON para permitir acceso público:
     ```json
    {
    	"Version": "2012-10-17",
    	"Statement": [
    		{
    			"Effect": "Allow",
    			"Action": [
    				"s3:PutObject",
    				"s3:DeleteObject",
    				"s3:GetObject"
    			],
    			"Resource": "arn:aws:s3:::epam-course1528/*"
    		}
    	]
    }
     ```


## 2. Crear un Repositorio en GitHub

1. **Inicia sesión en GitHub**:
   - Accede a [GitHub](https://github.com/) e inicia sesión con tus credenciales.

2. **Crear un Nuevo Repositorio**:
   - Haz clic en el icono **+** en la esquina superior derecha y selecciona **New repository**.
   - **Repository name**: Introduce un nombre para tu repositorio (por ejemplo, `my-static-website`).
   - **Description**: Opcionalmente, añade una descripción.
   - **Public**: Selecciona **Public** si deseas que el repositorio sea público.
   - Haz clic en **Create repository**.

3. **Añadir Archivos al Repositorio**:
   - Clona el repositorio localmente usando el comando:
     ```bash
     git clone https://github.com/Laurab1528/S3.git   ```
      
     git add index.html
     git commit -m "Add initial static website files"
     git push origin main
     ```

## 3. Configurar GitHub Actions para Desplegar en S3

1. **Configurar Secretos en GitHub**:
   - En tu repositorio en GitHub, ve a **Settings** > **Secrets and variables** > **Actions**.
   - Haz clic en **New repository secret** para añadir los siguientes secretos:
     - **Nombre del Secreto:** `AWS_ACCESS_KEY_ID`
       - **Valor del Secreto:** Tu `Access Key ID` de AWS.
     - **Nombre del Secreto:** `AWS_SECRET_ACCESS_KEY`
       - **Valor del Secreto:** Tu `Secret Access Key` de AWS.

2. **Crear el Archivo de Flujo de Trabajo**:
   - En tu repositorio, crea el directorio `.github/workflows` si no existe.
   - Dentro de este directorio, crea un archivo llamado `deploy.yml` con el siguiente contenido:
     ```yaml
     name: Deploy to S3

     on:
       push:
         branches:
           - main

     jobs:
       deploy:
         runs-on: ubuntu-latest

         steps:
           - name: Checkout code
             uses: actions/checkout@v3

           - name: Setup AWS CLI
             uses: aws-actions/configure-aws-credentials@v2
             with:
               aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
               aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
               aws-region: us-east-2

           - name: Sync files to S3
             run: |
               aws s3 sync . s3://epam-course1528 --exclude ".git/*" --exclude ".github/*" --delete
     ```


