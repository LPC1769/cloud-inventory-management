# cloud-inventory-management

## Descripción de módulos utilizados

Se utilizan los módulos `hashicorp/dir/template`, `terraform-aws-modules/vpc/aws` y un módulo propietario de nombre `web-app`.

## Componentes a analizar

- Lambda
- VPC Endpoint
- VPC
- Buckets S3
- DynamoDB
- CloudFront

## Descripción de meta-argumentos

### for_each
- Se utiliza para poder definir un local de las variables y definir los [índices](https://github.com/Khato1319/cloud-inventory-management/blob/main/iac/database.tf#L26) y [atributos](https://github.com/Khato1319/cloud-inventory-management/blob/main/iac/database.tf#L26) de la tabla a partir del mismo sin tener que repetir los nombres.
- Se utiliza en [```lambda.tf```](https://github.com/Khato1319/cloud-inventory-management/blob/main/iac/lambda.tf#LL51C7-L51C7) para poder iterar sobre archivos del directorio y comprimir todos los archivos de funciones lambda.
- Se utiliza en [```lambda.tf```](https://github.com/Khato1319/cloud-inventory-management/blob/main/iac/lambda.tf#L51) para poder iterar sobre archivos del directorio y publicar los lambdas de cada archivo.
- Se utiliza en [```cdn.tf```](https://github.com/Khato1319/cloud-inventory-management/blob/main/iac/web-app/cdn.tf#L24) para poder reusar la mayoría de la configuración de la distribución de CloudFront.
- Se utiliza en [```storage.tf```](https://github.com/Khato1319/cloud-inventory-management/blob/main/iac/web-app/storage.tf#L21) para poder reusar la configuración de los recursos asociados a los buckets raíz y de www.
- Se utiliza 3n [```storage.tf```](https://github.com/Khato1319/cloud-inventory-management/blob/main/iac/web-app/storage.tf#L28) para poder agreagr al bucket todos los archivos estáticos de la página web al bucket S3.
### depends_on
- Se utiliza en ```dynamo.tf``` para crear la tabla antes de crear los targets de lectura/escritura
- Se utiliza en ```dynamo.tf``` para crear los targets de lectura/escritura antes de la política de auto-scaling
- Se utiliza en ```storage.tf``` para crear la configuración pública de los buckets antes del ACL y las policies (ya que en el default privado no es posible configurar ACLs o policies). TODO chequear
### lifecycle
- Agrega una capa de seguridad extra en [```database.tf```](link) para evitar borrados accidentales del inventario de los clientes.
- Se utiliza en [```storage.tf```](link) para que un ```terraform apply``` en una arquitectura creada no aplique permisos públicos cuando el bucket es privado. El recurso se usa únicamente para permitir agregarle una política al bucket, pero el estado final del mismo queremos que sea privado.
## Descripción de funciones
### templatefile
- Se utiliza en [```storage.tf```](link) para poder reutilizar la configuración de una bucket policy cambiando únicamente ciertos parámetros como la distribución de CloudFront y el nombre del bucket.
### jsonencode
- Se utiliza en ```vpc-endpoint.tf``` en [aws_vpc_endpoint](www.link.com) y en ```vpc-endpoint``` en [aws_iam_policy](www.link.com).
### fileset
- Se utiliza en ```lambda.tf``` en [archive_file](link) y en [aws_lambda_function](link).
### split
- Se utiliza en ```lambda.tf``` en [archive_file](link) y en [aws_lambda_function](link).


TODO:
- Ver el tema de los valores del autoscaling de dynamo en database.tf
- Completar links de funciones y metaargumentos. -> Campa
- Chequear la configuracion de los recursos desde la consola de AWS.
- Armar el diagrama con los componentes de terraform (Lambda, VPC, VPC Endpoint, S3, Dynamo, CloudFront). -> Agus
