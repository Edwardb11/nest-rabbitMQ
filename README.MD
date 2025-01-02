# Documentación para el repositorio: nest-rabbitMQ

Este repositorio contiene una implementación básica de un sistema de microservicios utilizando NestJS y RabbitMQ para la comunicación entre servicios. RabbitMQ actúa como el broker de mensajes que permite la transmisión eficiente de datos entre productores y consumidores.

---

## **Estructura del Proyecto**

### **1. Configuración de RabbitMQ**
RabbitMQ es el broker de mensajes utilizado para manejar la comunicación entre microservicios. El proyecto está configurado para utilizar un servidor RabbitMQ en el host `localhost` con el puerto por defecto (5672).

#### **Configuración por defecto:**
- **Host:** `localhost`
- **Puerto:** `5672`
- **Credenciales:**
  - Usuario: `guest`
  - Contraseña: `guest`

Asegúrate de que RabbitMQ está instalado y en ejecución en tu sistema.

---

### **2. Código clave del repositorio**

#### **Productor**
El productor envía mensajes a RabbitMQ utilizando el módulo de microservicios de NestJS.

```typescript
@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Post('create-order')
  async createOrder(@Body() orderDto: OrderDto) {
    // Envía un evento de tipo 'order-placed'
    return this.appService.emitOrderPlaced(orderDto);
  }
}
```

#### **Consumidor**
El consumidor escucha mensajes de RabbitMQ y los procesa.

```typescript
@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @EventPattern('order-placed')
  handleOrderPlaced(@Payload() order: OrderDto) {
    return this.appService.handleOrderPlaced(order);
  }

  @MessagePattern({ cmd: 'fetch-orders' })
  getOrders(@Ctx() context: RmqContext) {
    console.log(context.getMessage());
    return this.appService.getOrders();
  }
}
```

---

### **3. Acceso a RabbitMQ**

#### **Acceso a la interfaz de administración**
RabbitMQ proporciona una interfaz web para administrar colas, intercambios y mensajes.

1. Habilita el plugin de administración (si no está activado):
   ```bash
   rabbitmq-plugins enable rabbitmq_management
   ```

2. Accede a la interfaz:
   - URL: [http://localhost:15672](http://localhost:15672)
   - Credenciales por defecto:
     - Usuario: `guest`
     - Contraseña: `guest`

3. Verifica que las colas e intercambios se estén creando correctamente.

---

### **4. Instalación y Ejecución del Proyecto**

#### **Requisitos previos**
- Node.js (v16 o superior)
- RabbitMQ instalado y funcionando
- Un gestor de paquetes como npm o yarn

#### **Pasos de instalación**
1. Clona el repositorio:
   ```bash
   git clone https://github.com/Edwardb11/nest-rabbitMQ.git
   cd nest-rabbitMQ
   ```

2. Instala las dependencias:
   ```bash
   npm install
   ```

3. Inicia la aplicación:
   ```bash
   npm run start:dev
   ```

---

### **5. Pruebas de la aplicación**

#### **Enviar un mensaje (productor):**
Puedes enviar mensajes utilizando herramientas como Postman o cURL:
```bash
POST http://localhost:3000/create-order
Content-Type: application/json

{
        "email": "edward@test.com",
        "productName": "Test",
        "quantity": "1"
}
```

#### **Escuchar mensajes (consumidor):**
El consumidor procesará automáticamente los eventos `order-placed` publicados en RabbitMQ. Los mensajes procesados aparecerán en los logs de la consola.

---

### **6. Solución de problemas**

1. **RabbitMQ no está funcionando:**
   - Verifica que RabbitMQ está instalado y corriendo:
     ```bash
     rabbitmqctl status
     ```

2. **Puertos ocupados:**
   - Asegúrate de que los puertos `5672` y `15672` están libres.
   - Usa un comando como `lsof -i :5672` para verificar qué proceso está ocupando el puerto.

3. **Mensajes no entregados:**
   - Verifica la configuración de las colas e intercambios en la interfaz web de RabbitMQ.
   - Asegúrate de que los nombres de los eventos coincidan entre el productor y el consumidor.

---

### **7. Tecnologías utilizadas**
- **NestJS**: Framework para construir aplicaciones de servidor escalables.
- **RabbitMQ**: Broker de mensajes para la comunicación entre microservicios.
- **TypeScript**: Lenguaje principal para el desarrollo.

---

