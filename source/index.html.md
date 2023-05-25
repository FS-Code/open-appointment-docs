---
title: API Reference

language_tabs:
  - json

toc_footers:

includes:
  

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Open Appointment
---

# Introduction

This is documentation for Open Appointment. Open Appointment is open-source appointment reservation system written by "Code Marathon 2023" participants.

# Authorization

## Login

> Request:

```json
{
  "email": "user1@example.org",
  "password": "12345678"
}
```

> Response:

```json
{
  "data": {
    "user": {
      "id": 1,
      "email": "user1@example.org"
    }
  }
}
```

This endpoint allows user to login

---

1. Staff enters email & password and clicks "Login" button.
2. Frontend sends these values to `/api/login`.
3. Backend checks if email & password is correct:
   1. On success: Backend should generate unique token and store it in cookie & database too. So later auth-required endpoints can be requested. If response contains user object then frontend should navigate to `/dashboard`.
   2. On failure: Should return error message ("Email or password is incorrect")

---


### HTTP Request

`POST /api/login`

### Parameters

| Parameter | Type   | Description                      |
|-----------|--------|----------------------------------|
| email     | string | Email address of registered user |
| password  | string | Password of registered user      |

## Register

> Request:

```json
{
  "email": "email@example.org",
  "password": "12345678"
}
```

> Response:

```json
{
  "data": {
    "user": {
      "id": 1,
      "email": "user1@example.org"
    }
  }
}
```

This endpoint allows user to register.

---

1. Staff enters email, password & password repeat and clicks "Create Account" button.
2. Frontend sends these values (except password repeat) to `/api/register`.
3. Backend checks if email does not already exist and password meets requirements:
   1. On success: Backend should create a staff and also generate token to store it in cookie & database . So later auth-required endpoints can be requested. If response contains user object then frontend should navigate to `/dashboard`.
   2. On failure: Should return error message ("Email is already exists", "Password does not meet requirements")

---

### HTTP Request

`POST /api/register`

### Parameters

| Parameter | Type   | Description   |
|-----------|--------|---------------|
| email     | string | Email address |
| password  | string | Password      |

## Reset password

> Request:

```json
{
  "email": "user1@example.org"
}
```

> Response:

```json
{
  "data": {}
}
```

This endpoint allows user to get reset password link.

`POST /api/get-reset-password-link`

### Parameters

| Parameter | Type   | Description   |
|-----------|--------|---------------|
| email     | string | Email address |

# Services

## Get Services

> Request:

```json
{
  "cursor": null
}
```

> Response:

```json
{
  "data": {
    "services": [
      {
        "id": 1,
        "name": "Actor Training",
        "location": "Hollywood",
        "details": "Get an hourly actor training from Jim Carry",
        "duration": 3600,
        "business_hours": {
          "monday": {
            "start": "10:00",
            "ends": "19:00"
          },
          "tuesday": {
            "start": "10:00",
            "ends": "19:00"
          },
          "wednesday": {
            "start": "10:00",
            "ends": "19:00"
          },
          "thursday": {
            "start": "10:00",
            "ends": "19:00"
          },
          "friday": {
            "start": "10:00",
            "ends": "19:00"
          },
          "saturday": null,
          "sunday": null
        },
        "buffer": {
          "before": 0,
          "after": 1800
        }
      }
    ],
    "cursor": 11
  }
}
```

This endpoint fetches current user's services from database. Returns `cursor` null if there is not next page, otherwise `id` of first service from the next page

`POST /api/get-services`

### Parameters

| Parameter | Type       | Description                                               |
|-----------|------------|-----------------------------------------------------------|
| `cursor`  | id or null | ID of service to start from. Send null for the first time |

## Create Service

> Request:

```json
{
  "name": "Actor Training",
  "location": "Hollywood",
  "details": "Get an hourly actor training from Jim Carry",
  "duration": 3600,
  "business_hours": {
    "monday": {
      "start": "10:00",
      "ends": "19:00"
    },
    "tuesday": {
      "start": "10:00",
      "ends": "19:00"
    },
    "wednesday": {
      "start": "10:00",
      "ends": "19:00"
    },
    "thursday": {
      "start": "10:00",
      "ends": "19:00"
    },
    "friday": {
      "start": "10:00",
      "ends": "19:00"
    },
    "saturday": null,
    "sunday": null
  },
  "buffer": {
    "before": 0,
    "after": 1800
  }
}
```

> Response:

```json
{
  "data": {
    "id": 1
  }
}
```

This endpoint allows to create new service.

`POST /api/create-service`

### Parameters

| Parameter      | Type                               | Description                          |
|----------------|------------------------------------|--------------------------------------|
| name           | string                             | Service's name                       |
| location       | string                             | Location of service                  |
| details        | string                             | Notes about service, can be empty    |
| duration       | int                                | Duration of service in seconds       |
| business_hours | [#business_hours](#business_hours) | Working hours of service             |
| buffer         | [#buffer](#buffer)                 | Waiting time before or after service |

### business_hours

| Parameter | Type                         | Description           |
|-----------|------------------------------|-----------------------|
| monday    | [#weekday](#weekday) or null | null if it is day-off |
| tuesday   | [#weekday](#weekday) or null | null if it is day-off |
| wednesday | [#weekday](#weekday) or null | null if it is day-off |
| thursday  | [#weekday](#weekday) or null | null if it is day-off |
| friday    | [#weekday](#weekday) or null | null if it is day-off |
| saturday  | [#weekday](#weekday) or null | null if it is day-off |
| sunday    | [#weekday](#weekday) or null | null if it is day-off |

### weekday

| Parameter | Type   | Description                                   |
|-----------|--------|-----------------------------------------------|
| starts    | string | starting time of working hours, in H:i format |
| ends      | string | ending time of working hours, in H:i format   |

### buffer
| Parameter | Type | Description                              |
|-----------|------|------------------------------------------|
| before    | int  | waiting time before a service in seconds |
| after     | int  | waiting time after a service in seconds  |

## Edit Service

> Request:

```json
{
  "id": 1,
  "name": "Actor Training",
  "location": "Hollywood",
  "details": "Get two hourly actor training from Jim Carry",
  "duration": 7200,
  "business_hours": {
    "monday": {
      "start": "11:00",
      "ends": "20:00"
    },
    "tuesday": {
      "start": "11:00",
      "ends": "20:00"
    },
    "wednesday": {
      "start": "11:00",
      "ends": "20:00"
    },
    "thursday": {
      "start": "11:00",
      "ends": "20:00"
    },
    "friday": null,
    "saturday": null,
    "sunday": null
  },
  "buffer": {
    "before": 0,
    "after": 0
  }
}
```

> Response:

```json
{
  "data": {}
}
```

This endpoint allows to edit existing service.

`POST /api/edit-service`

### Parameters

| Parameter      | Type                               | Description                          |
|----------------|------------------------------------|--------------------------------------|
| id             | int                                | ID of existing service               |
| name           | string                             | Service's name                       |
| location       | string                             | Location of service                  |
| details        | string                             | Notes about service, can be empty    |
| duration       | int                                | Duration of service in seconds       |
| business_hours | [#business_hours](#business_hours) | Working hours of service             |
| buffer         | [#buffer](#buffer)                 | Waiting time before or after service |

### business_hours

| Parameter | Type                         | Description           |
|-----------|------------------------------|-----------------------|
| monday    | [#weekday](#weekday) or null | null if it is day-off |
| tuesday   | [#weekday](#weekday) or null | null if it is day-off |
| wednesday | [#weekday](#weekday) or null | null if it is day-off |
| thursday  | [#weekday](#weekday) or null | null if it is day-off |
| friday    | [#weekday](#weekday) or null | null if it is day-off |
| saturday  | [#weekday](#weekday) or null | null if it is day-off |
| sunday    | [#weekday](#weekday) or null | null if it is day-off |

### weekday

| Parameter | Type   | Description                                   |
|-----------|--------|-----------------------------------------------|
| starts    | string | starting time of working hours, in H:i format |
| ends      | string | ending time of working hours, in H:i format   |

### buffer
| Parameter | Type | Description                              |
|-----------|------|------------------------------------------|
| before    | int  | waiting time before a service in seconds |
| after     | int  | waiting time after a service in seconds  |

## Delete Services

> Request:

```json
{
  "ids": [1]
}
```

> Response:

```json
{
  "data": {}
}
```

This endpoint allows to delete existing services.

`POST /api/delete-services`

### Parameters

| Parameter | Type  | Description                   |
|-----------|-------|-------------------------------|
| ids       | int[] | ID array of existing services |

# Appointments

## Get Appointments

> Request:

```json
{
  "cursor": 5
}
```

> Response:

```json
{
  "data": {
    "appointments": [
      {
        "id": 5,
        "service": {
          "id": 1,
          "name": "Actor Training",
          "location": "Hollywood",
          "details": "Get an hourly actor training from Jim Carry",
          "duration": 3600,
          "business_hours": {
            "monday": {
              "start": "10:00",
              "ends": "19:00"
            },
            "tuesday": {
              "start": "10:00",
              "ends": "19:00"
            },
            "wednesday": {
              "start": "10:00",
              "ends": "19:00"
            },
            "thursday": {
              "start": "10:00",
              "ends": "19:00"
            },
            "friday": {
              "start": "10:00",
              "ends": "19:00"
            },
            "saturday": null,
            "sunday": null
          },
          "buffer": {
            "before": 0,
            "after": 1800
          }
        },
        "customer": {
          "name": "Ulrich Nielsen",
          "email": "customer1@example.org"
        },
        "starts_at": "",
        "ends_at": "",
        "created_at": ""
      }
    ],
    "cursor": 16
  }
}
```

This endpoint fetches current user's appointments from database. Returns `cursor` null if there is not next page, otherwise id of first appointment from the next page

`POST /api/get-appointments`

### Parameters

| Parameter | Type       | Description                                                   |
|-----------|------------|---------------------------------------------------------------|
| cursor    | id or null | ID of appointment to start from. Send null for the first time |

## Get Timeslots

> Request:

```json
{
  "service_id": 5,
  "start_from": "2023-05-24"
}
```
 
> Response:

```json
{
  "dates": {
    "2023-05-24": [
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "10:00",
        "end_time": "11:00"
      },
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "11:00",
        "end_time": "12:00"
      },
      "...",
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "18:00",
        "end_time": "19:00"
      }
    ],
    "2023-05-25": [
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "11:30",
        "end_time": "12:30"
      },
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "12:30",
        "end_time": "13:30"
      },
      "...",
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "17:30",
        "end_time": "18:30"
      }
    ],
    "dates_bla_bla": "...",
    "2023-05-31": [
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "11:30",
        "end_time": "12:30"
      },
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "12:30",
        "end_time": "13:30"
      },
      "...",
      {
        "buffer_before": 0,
        "buffer_after": 1800,
        "start_time": "17:30",
        "end_time": "18:30"
      }
    ]
  }
}
```

This endpoint fetches timeslots to choose for which time user wants to book an appointment. Returns all the timeslots for the specified service from starting time till the end of month. If start_time param is null, then it will return from the current months first day.

`POST /api/get-timeslots`

### Parameters

| Parameter  | Type           | Description                                                           |
|------------|----------------|-----------------------------------------------------------------------|
| service_id | int            | ID of the service                                                     |
| start_from | string or null | Date to start from. If NULL then start from current months first date |


<aside class="notice">
When returning the timeslots, existing appointments also should be considered. When there's an existing appointment their total reserve time should be excluded ( including buffers )
</aside>

## Create Appointment

> Request:

```json
{
  "service_id": 1,
  "customer": {
    "name": "Ulrich Nielsen",
    "email": "customer1@example.org"
  },
  "starts_at": "",
  "ends_at": "",
  "created_at": ""
}
```

> Response:

```json
{
  "data": {
    
  }
}
```

This endpoint allows customers to book an appointment

`POST /api/create-appointment`

### Parameters

| Parameter   | Type                   | Description                          |
|-------------|------------------------|--------------------------------------|
| service_id  | int                    | ID of service                        |
| customer    | [#customer](#customer) |                                      |
| starts_at   | timestamp              | Starting timestamp of an appointment |
| ends_at     | timestamp              | Ending timestamp of an appointment   |

### customer

| Parameter | Type   | Description       |
|-----------|--------|-------------------|
| name      | string | Name of customer  |
| email     | string | Email of customer |

## Delete Appointments

> Request:

```json
{
  "ids": [1]
}
```

> Response:

```json
{
  "data": {}
}
```

This endpoint allows to delete appointments.

`POST /api/delete-appointments`
