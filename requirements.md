# Airbnb Clone Backend Requirements

## 1. User Authentication

- Allows users to sign up (as Guests or Hosts), log in, and manage their profiles securely.
- Supports email/password login and uses a token for secure access (based on JWT from the original Task 1).

### API Endpoints

#### Sign Up
- **Address**: `POST /users/`
- **What It Does**: Creates a new user account.
- **Input**:
  ```json
  {
    "email": "user@example.com",
    "password": "password123",
    "name": "John Doe",
    "role": "guest" // or "host"
  }
  ```
  - **Checks**:
    - `email`: Must be a valid email (e.g., has "@" and a domain like ".com").
    - `password`: At least 6 characters.
    - `name`: Cannot be empty.
    - `role`: Must be "guest" or "host".
- **Output**:
  - **Success**:
    ```json
    {
      "message": "User created!",
      "user": {
        "id": "123",
        "email": "user@example.com",
        "name": "John Doe",
        "role": "guest"
      },
      "token": "abc123" // Token for secure access
    }
    ```
  - **Error**:
    ```json
    {
      "error": "Email already used or invalid data"
    }
    ```

#### Log In
- **Address**: `POST /users/login`
- **What It Does**: Lets a user log in with email and password.
- **Input**:
  ```json
  {
    "email": "user@example.com",
    "password": "password123"
  }
  ```
  - **Checks**:
    - `email`: Must match a user in the system.
    - `password`: Must be correct for that email.
- **Output**:
  - **Success**:
    ```json
    {
      "message": "Login successful!",
      "user": {
        "id": "123",
        "email": "user@example.com",
        "name": "John Doe"
      },
      "token": "abc123"
    }
    ```
  - **Error**:
    ```json
    {
      "error": "Wrong email or password"
    }
    ```

#### Update Profile
- **Address**: `PUT /users/{user_id}/`
- **What It Does**: Updates a user’s profile information.
- **Input**:
  ```json
  {
    "name": "Jane Doe",
    "email": "jane@example.com"
  }
  ```
  - **Checks**:
    - `user_id`: Must match the logged-in user.
    - `name`: Cannot be empty.
    - `email`: Must be a valid email and not used by another user.
- **Output**:
  - **Success**:
    ```json
    {
      "message": "Profile updated!",
      "user": {
        "id": "123",
        "email": "jane@example.com",
        "name": "Jane Doe"
      }
    }
    ```
  - **Error**:
    ```json
    {
      "error": "Invalid data or not allowed"
    }
    ```
## 2. Property Management

- Lets Hosts create, update, or delete property listings (e.g., homes or apartments).
- Lets Guests search and view available properties.

### API Endpoints

#### Create Property
- **Address**: `POST /properties/`
- **What It Does**: Host adds a new property listing.
- **Input**:
  ```json
  {
    "title": "Sunny Apartment",
    "description": "Bright place in the city",
    "city": "Nairobi",
    "price_per_night": 50,
    "max_guests": 3,
    "amenities": ["Wi-Fi", "Parking"]
  }
  ```
  - **Checks**:
    - `title`: Cannot be empty, max 50 characters.
    - `description`: Cannot be empty, max 200 characters.
    - `city`: Cannot be empty.
    - `price_per_night`: Must be a positive number.
    - `max_guests`: Must be a positive number.
    - `amenities`: List of valid options (e.g., "Wi-Fi", "Parking").
    - User must be a Host.
- **Output**:
  - **Success**:
    ```json
    {
      "message": "Property added!",
      "property": {
        "id": "456",
        "title": "Sunny Apartment",
        "city": "Nairobi",
        "price_per_night": 50,
        "max_guests": 3
      }
    }
    ```
  - **Error**:
    ```json
    {
      "error": "Invalid data or not a host"
    }
    ```

#### Search Properties
- **Address**: `GET /properties/?city=Nairobi&max_price=100`
- **What It Does**: Guests search properties by city or price.
- **Input** (in URL):
  - `city`: Optional, city name (e.g., "Nairobi").
  - `max_price`: Optional, maximum price per night.
- **Output**:
  - **Success**:
    ```json
    {
      "properties": [
        {
          "id": "456",
          "title": "Sunny Apartment",
          "city": "Nairobi",
          "price_per_night": 50,
          "max_guests": 3
        }
      ]
    }
    ```
  - **Error**:
    ```json
    {
      "error": "Invalid search terms"
    }
    ```

#### Update Property
- **Address**: `PUT /properties/{property_id}/`
- **What It Does**: Host updates an existing property.
- **Input**:
  ```json
  {
    "title": "Updated Sunny Apartment",
    "price_per_night": 60
  }
  ```
  - **Checks**:
    - `property_id`: Must be a valid property owned by the Host.
    - Fields: Same checks as Create Property, but only for provided fields.
- **Output**:
  - **Success**:
    ```json
    {
      "message": "Property updated!",
      "property": {
        "id": "456",
        "title": "Updated Sunny Apartment",
        "price_per_night": 60
      }
    }
    ```
  - **Error**:
    ```json
    {
      "error": "Property not found or not allowed"
    }
    ```


## 3. Booking System

- Lets Guests book a property for specific dates.
- Checks that the property is available and prevents overlapping bookings.
- Allows Guests or Hosts to cancel bookings.

### API Endpoints

#### Create Booking
- **Address**: `POST /bookings/`
- **What It Does**: Guest books a property for chosen dates.
- **Input**:
  ```json
  {
    "property_id": "456",
    "start_date": "2025-07-01",
    "end_date": "2025-07-05",
    "guests": 2
  }
  ```
  - **Checks**:
    - `property_id`: Must be a valid property.
    - `start_date`, `end_date`: Valid dates, start_date before end_date.
    - `guests`: Positive number, not more than the property’s max_guests.
    - Dates must not overlap with other bookings.
    - User must be a Guest.
- **Output**:
  - **Success**:
    ```json
    {
      "message": "Booking created!",
      "booking": {
        "id": "789",
        "property_id": "456",
        "start_date": "2025-07-01",
        "end_date": "2025-07-05",
        "guests": 2,
        "status": "confirmed"
      }
    }
    ```
  - **Error**:
    ```json
    {
      "error": "Property not available or invalid data"
    }
    ```

#### Cancel Booking
- **Address**: `DELETE /bookings/{booking_id}/`
- **What It Does**: Guest or Host cancels a booking.
- **Input**: None (needs user to be logged in with a token).
- **Checks**:
  - `booking_id`: Must be a valid booking.
  - User must be the Guest who booked or the Host of the property.
- **Output**:
  - **Success**:
    ```json
    {
      "message": "Booking canceled!",
      "booking": {
        "id": "789",
        "status": "canceled"
      }
    }
    ```
  - **Error**:
    ```json
    {
      "error": "Booking not found or not allowed"
    }
    ```
