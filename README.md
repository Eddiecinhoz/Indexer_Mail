# Indexer_Mail

## Backend
**Nota** Para la ejecución del backend es necesaria la instalación y corrrecta ejecución de las herramientas: GOLang y Zincsearch.

### Instrucciones:
1. Montar en el local la herramienta Zincsearch con el comando recomendado: *FIRST_ADMIN_USER=admin FIRST_ADMIN_PASSWORD=Complexpass#123 zincsearch*
2. Descargar la base de datos de correos enron: http://www.cs.cmu.edu/~enron/enron_mail_20110402.tgz
3. Descomprimir la carpeta y ubicarla en la ruta: *".../Indexer_Mail/Backend/indexer/"* junto con el archivo *Indexer.go*
4. Ejecutar el comanto en terminal ./indexer (Indexara y cargar la base de datos a Zincsearch)
5. Una vez terminada la ejecución ejecutar en el terminal el archivo llamado *router(./router)* ubicado en la ruta *".../Indexer_Mail/Backend/router/"*
6. Una vez el script se esta ejecutando se puede realizar el despliegue del **Frontend**

## Frontend
1. Ingresar por terminal a la ruta *".../Indexer_Mail/Frontend/email-front/"*
2. Una vez dentro instalar todas la dependecias necesarias para la ejecución del codigo con el comando: *npm i*
3. Cuando finalice la instalación el proyecto de Vue podra ejecutarse
4. Ejecutar el proyecto usando el comando: *npm run dev*
5. Termina de compilarse el proyecto podra ingresar a el a partir de la ruta indicada en consola

*Si consigue seguir todos los pasos sera posible interactuar con la base de datos desde*

De llegar a ser necesario en este link encontrara un video donde se explica todo lo anterior:
https://vimeo.com/manage/videos/908511602/2e5ac42ebb?studio_recording=true&record_session_id=23a22e91-856a-425e-b188-6640e3af0206
