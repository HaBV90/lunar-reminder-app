# Controller Template

## Basic CRUD Controller

```typescript
// modules/events/events.controller.ts
import {
  Controller,
  Get,
  Post,
  Put,
  Delete,
  Body,
  Param,
  Query,
  UseGuards,
  HttpStatus,
} from '@nestjs/common';
import { ApiTags, ApiOperation, ApiResponse, ApiBearerAuth } from '@nestjs/swagger';
import { JwtAuthGuard } from '@common/guards/jwt-auth.guard';
import { CurrentUser } from '@common/decorators/current-user.decorator';
import { User } from '@modules/users/entities/user.entity';
import { EventsService } from './events.service';
import { CreateEventDto } from './dto/create-event.dto';
import { UpdateEventDto } from './dto/update-event.dto';
import { FindEventsDto } from './dto/find-events.dto';

@ApiTags('events')
@ApiBearerAuth()
@UseGuards(JwtAuthGuard)
@Controller('events')
export class EventsController {
  constructor(private readonly eventsService: EventsService) {}

  @Get()
  @ApiOperation({ summary: 'Get all events for current user' })
  @ApiResponse({ status: HttpStatus.OK, description: 'Events retrieved' })
  async findAll(
    @CurrentUser() user: User,
    @Query() query: FindEventsDto,
  ) {
    return this.eventsService.findAll(user.id, query);
  }

  @Get(':id')
  @ApiOperation({ summary: 'Get event by ID' })
  @ApiResponse({ status: HttpStatus.OK, description: 'Event retrieved' })
  @ApiResponse({ status: HttpStatus.NOT_FOUND, description: 'Not found' })
  async findOne(
    @CurrentUser() user: User,
    @Param('id') id: string,
  ) {
    return this.eventsService.findOne(id, user.id);
  }

  @Post()
  @ApiOperation({ summary: 'Create a new event' })
  @ApiResponse({ status: HttpStatus.CREATED, description: 'Event created' })
  @ApiResponse({ status: HttpStatus.BAD_REQUEST, description: 'Invalid input' })
  async create(
    @CurrentUser() user: User,
    @Body() createEventDto: CreateEventDto,
  ) {
    return this.eventsService.create(createEventDto, user.id);
  }

  @Put(':id')
  @ApiOperation({ summary: 'Update an event' })
  @ApiResponse({ status: HttpStatus.OK, description: 'Event updated' })
  @ApiResponse({ status: HttpStatus.NOT_FOUND, description: 'Not found' })
  async update(
    @CurrentUser() user: User,
    @Param('id') id: string,
    @Body() updateEventDto: UpdateEventDto,
  ) {
    return this.eventsService.update(id, updateEventDto, user.id);
  }

  @Delete(':id')
  @ApiOperation({ summary: 'Delete an event' })
  @ApiResponse({ status: HttpStatus.OK, description: 'Event deleted' })
  @ApiResponse({ status: HttpStatus.NOT_FOUND, description: 'Not found' })
  async remove(
    @CurrentUser() user: User,
    @Param('id') id: string,
  ) {
    return this.eventsService.remove(id, user.id);
  }
}
```

---

## Key Decorators

| Decorator | Purpose |
|-----------|---------|
| `@Controller('path')` | Define route prefix |
| `@UseGuards()` | Apply authentication |
| `@Get/@Post/@Put/@Delete` | HTTP methods |
| `@Body()` | Parse request body |
| `@Param('id')` | Extract URL parameter |
| `@Query()` | Parse query string |
| `@CurrentUser()` | Get authenticated user |
