A Arquitetura Limpa (Clean Architecture) é um conjunto de práticas de design de software que busca criar sistemas modulares e independentes de detalhes de implementação. Essa abordagem foi proposta por Robert C. Martin e tem como objetivo principal separar as preocupações em diferentes camadas, promovendo a facilidade de manutenção, testabilidade e flexibilidade do código.

<div align="center" >
  <img src="./github/clean-architecture.png" alt="clean-architecture" height="425">
</div>

Vamos explorar como você pode aplicar os princípios da Arquitetura Limpa em um projeto Node.js com TypeScript.

### Componentes da Clean Architecture:

1. **Entidades (Entities):** Representam conceitos de negócio. Elas são a camada mais interna e não dependem de nenhum detalhe da implementação.

2. **Casos de Uso (Use Cases):** Definem as regras de negócio e orquestram a interação entre entidades. Eles são independentes de qualquer framework ou detalhe de interface do usuário.

3. **Controladores (Controllers):** Responsáveis por lidar com as requisições HTTP ou eventos de interface do usuário. Eles traduzem os detalhes da interface para chamadas aos casos de uso.

4. **Gateways de Interface (Interface Adapters):** Traduzem dados entre os formatos esperados pelas entidades e os formatos utilizados pelos controladores. Também implementam mecanismos de armazenamento e recuperação de dados.

5. **Frameworks e Drivers (Frameworks and Drivers):** Na camada mais externa, ficam os detalhes de infraestrutura e ferramentas. Isso inclui frameworks, bibliotecas e drivers de banco de dados.

### Estrutura do Projeto:

A estrutura do projeto pode seguir a hierarquia da Clean Architecture. Aqui está um exemplo:

```plaintext
/src
|-- entities
|-- usecases
|-- controllers
|-- interfaces
|-- frameworks_drivers
|-- main
|-- index.ts
```

### Exemplo de Implementação:

Vamos criar uma aplicação simples de lista de tarefas para ilustrar os conceitos.

1. **Entities:**
   ```typescript
   // entities/task.entity.ts
   export class Task {
     constructor(public id: string, public description: string, public completed: boolean) {}
   }
   ```

2. **Use Cases:**
   ```typescript
   // usecases/task.usecase.ts
   import { Task } from '../entities/task.entity';

   export interface TaskUseCase {
     createTask(description: string): Task;
     getTaskById(id: string): Task | null;
   }

   export class TaskUseCaseImpl implements TaskUseCase {
     private tasks: Task[] = [];

     createTask(description: string): Task {
       const task = new Task(String(this.tasks.length + 1), description, false);
       this.tasks.push(task);
       return task;
     }

     getTaskById(id: string): Task | null {
       return this.tasks.find(task => task.id === id) || null;
     }
   }
   ```

3. **Controllers:**
   ```typescript
   // controllers/task.controller.ts
   import { TaskUseCase } from '../usecases/task.usecase';

   export class TaskController {
     constructor(private taskUseCase: TaskUseCase) {}

     createTask(description: string) {
       const task = this.taskUseCase.createTask(description);
       return task;
     }

     getTaskById(id: string) {
       const task = this.taskUseCase.getTaskById(id);
       return task;
     }
   }
   ```

4. **Interfaces:**
   ```typescript
   // interfaces/task.interface.ts
   import { Task } from '../entities/task.entity';

   export interface TaskRepository {
     save(task: Task): void;
     findById(id: string): Task | null;
   }
   ```

5. **Frameworks e Drivers:**
   ```typescript
   // frameworks_drivers/task.repository.ts
   import { Task } from '../entities/task.entity';
   import { TaskRepository } from '../interfaces/task.interface';

   export class InMemoryTaskRepository implements TaskRepository {
     private tasks: Task[] = [];

     save(task: Task): void {
       this.tasks.push(task);
     }

     findById(id: string): Task | null {
       return this.tasks.find(task => task.id === id) || null;
     }
   }
   ```

6. **Main:**
   ```typescript
   // main.ts
   import { TaskUseCaseImpl } from './usecases/task.usecase';
   import { TaskController } from './controllers/task.controller';
   import { InMemoryTaskRepository } from './frameworks_drivers/task.repository';

   const taskRepository = new InMemoryTaskRepository();
   const taskUseCase = new TaskUseCaseImpl(taskRepository);
   const taskController = new TaskController(taskUseCase);

   // Exemplo de uso
   const createdTask = taskController.createTask('Complete Clean Architecture tutorial');
   console.log('Task created:', createdTask);

   const fetchedTask = taskController.getTaskById(createdTask.id);
   console.log('Fetched task by ID:', fetchedTask);
   ```

<a href="https://raw.githubusercontent.com/ARTHURPC03/Proffy-FullStack/master/github/linkedin.png">
<img src="https://raw.githubusercontent.com/ARTHURPC03/Proffy-FullStack/master/github/linkedin.png" alt="linkedin" height="50"></a>


Veja meu Linkedin: [Leandro Dantas](https://www.linkedin.com/in/leandro-dantas-1959b711b/)