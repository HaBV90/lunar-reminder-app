# DTO Template

## Create DTO with Validation

```typescript
// modules/events/dto/create-event.dto.ts
import {
  IsString,
  IsNotEmpty,
  IsEnum,
  IsOptional,
  IsInt,
  IsBoolean,
  IsDateString,
  IsArray,
  IsUUID,
  Min,
  Max,
  MaxLength,
  ValidateIf,
  ValidateNested,
} from 'class-validator';
import { Type } from 'class-transformer';
import { ApiProperty, ApiPropertyOptional } from '@nestjs/swagger';
import { EventType, CalendarType, LeapOption } from '@common/enums';

class TimeSlotDto {
  @ApiProperty({ minimum: 0, maximum: 23 })
  @IsInt()
  @Min(0)
  @Max(23)
  hour: number;

  @ApiProperty({ minimum: 0, maximum: 59 })
  @IsInt()
  @Min(0)
  @Max(59)
  minute: number;
}

export class CreateEventDto {
  @ApiProperty({ maxLength: 200 })
  @IsString()
  @IsNotEmpty()
  @MaxLength(200)
  name: string;

  @ApiProperty({ enum: EventType })
  @IsEnum(EventType)
  type: EventType;

  @ApiProperty({ enum: CalendarType })
  @IsEnum(CalendarType)
  calendarType: CalendarType;

  @ApiPropertyOptional({ minimum: 1, maximum: 30 })
  @ValidateIf((o) => o.calendarType === CalendarType.LUNAR)
  @IsInt()
  @Min(1)
  @Max(30)
  lunarDay?: number;

  @ApiPropertyOptional({ minimum: 1, maximum: 12 })
  @ValidateIf((o) => o.calendarType === CalendarType.LUNAR)
  @IsInt()
  @Min(1)
  @Max(12)
  lunarMonth?: number;

  @ApiPropertyOptional()
  @IsOptional()
  @IsBoolean()
  lunarIsLeap?: boolean;

  @ApiPropertyOptional({ enum: LeapOption })
  @IsOptional()
  @IsEnum(LeapOption)
  lunarLeapOption?: LeapOption;

  @ApiPropertyOptional()
  @IsOptional()
  @IsDateString()
  solarDate?: string;

  @ApiPropertyOptional()
  @IsOptional()
  @IsString()
  @MaxLength(500)
  notes?: string;

  @ApiPropertyOptional({ default: true })
  @IsOptional()
  @IsBoolean()
  notifyEnabled?: boolean;

  @ApiPropertyOptional({ default: 3 })
  @IsOptional()
  @IsInt()
  @Min(0)
  @Max(30)
  notifyDaysBefore?: number;

  @ApiPropertyOptional({ type: [TimeSlotDto] })
  @IsOptional()
  @IsArray()
  @ValidateNested({ each: true })
  @Type(() => TimeSlotDto)
  notifyTimeSlots?: TimeSlotDto[];

  @ApiPropertyOptional({ type: [String] })
  @IsOptional()
  @IsArray()
  @IsUUID('4', { each: true })
  groupIds?: string[];
}
```

---

## Update DTO (Partial)

```typescript
// modules/events/dto/update-event.dto.ts
import { PartialType } from '@nestjs/swagger';
import { CreateEventDto } from './create-event.dto';

export class UpdateEventDto extends PartialType(CreateEventDto) {}
```

---

## Query DTO

```typescript
// modules/events/dto/find-events.dto.ts
import { IsOptional, IsInt, IsEnum, IsUUID, IsString, Min, Max } from 'class-validator';
import { Type } from 'class-transformer';
import { ApiPropertyOptional } from '@nestjs/swagger';
import { EventType } from '@common/enums';

export class FindEventsDto {
  @ApiPropertyOptional({ default: 1 })
  @IsOptional()
  @Type(() => Number)
  @IsInt()
  @Min(1)
  page?: number = 1;

  @ApiPropertyOptional({ default: 20 })
  @IsOptional()
  @Type(() => Number)
  @IsInt()
  @Min(1)
  @Max(100)
  limit?: number = 20;

  @ApiPropertyOptional({ enum: EventType })
  @IsOptional()
  @IsEnum(EventType)
  type?: EventType;

  @ApiPropertyOptional()
  @IsOptional()
  @IsUUID()
  groupId?: string;

  @ApiPropertyOptional()
  @IsOptional()
  @IsString()
  search?: string;
}
```

---

## Common Validators

| Decorator | Purpose |
|-----------|---------|
| `@IsString()` | Must be string |
| `@IsNotEmpty()` | Cannot be empty |
| `@IsOptional()` | Field is optional |
| `@IsEnum(Enum)` | Must be enum value |
| `@IsInt()` | Must be integer |
| `@Min(n)` / `@Max(n)` | Number bounds |
| `@MaxLength(n)` | String length |
| `@IsUUID()` | Valid UUID |
| `@ValidateIf()` | Conditional validation |
