---
title: Logging
tags:
  - nestjs
---
## Types of Log

- Log: 중요한 정보를 로깅하기 위한 일반적인 목적을 가짐
- Warning: 치명적이거나 중단 될 정도가 아닌 이슈를 경고하기 위한 목적
- Error: 치명적이거나 중단 될 정도의 이슈를 제공
- Debug: Error나 Warning의 경우에 대해 디버깅하는데 도움을 주는 중요한 정보
- Verbose: 어플리케이션의 동작에 대한 insight를 제공

![[Pasted image 20240303182615.png]]

## Logging 적용

적용하려고 하는 곳에 Logger를 import하여 사용한다. 

```ts title="main.ts"
import {Logger} from '@nestjs/common'
async function bootstrap() {
	const logger = new Logger('boobtstrap');
}
```

Verbose, Error, Debug 적용은 각각 아래와 같다.

```ts title="TasksController.ts"
export class TasksController {
	private logger = new Logger('TaskController');
	
	@Post()
	@UsePipes(ValidationPipe)
	async createTask(@Body() createTaskDto: CreateTaskDto,
		@GetUser() user: User): Promise<Task> {
			this.logger.verbose(`User "${user.username}" create a new task, filters: ${JSON.stringify(createTaskDto)}`);
			return await this.taskService.createTask(createTaskDto, user);
}}
```

```ts title="TaskService.ts"
async getTasks(filterDto: GetTasksFilterDto,
	user: User): Promise<Task[]> {
		//...중략
		this.logger.error(`Failed to get tasks for user "${user.username}", Filters: ${JSON.stringify(filterDto)}`, error.stack)
}
```

```ts title="AuthService.ts"
async signIn(authCredentialsDto: AuthCredentialsDto): Promise<{accessToken: string}> {
	//...중략
	const payload: JwtPayload = {username};
	this.logger.debug(`Generated JWT Token with payload : ${JSON.stringify(payload)}`);
	return {accessToken};
}
```