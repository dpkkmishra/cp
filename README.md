## 🎯 Core Business Entities

### 👥 User Management

#### **User Model**
**Purpose:** Core user entity representing all platform users

**Key Fields for UI:**
- `role`: Determines user permissions and dashboard access
  - `superadmin`: Full system access
  - `manager`: System-wide management
  - `tenant_admin`: Venue administrator
  - `location_manager`: Single location management
  - `customer`: End user/player
  - `coach`: Sports instructor
  - `staff`: General staff member

- `status`: User account state
  - `active`: Can use the platform
  - `inactive`: Temporarily disabled
  - `blocked`: Permanently restricted

- `onboarding_completed`: Shows if user needs onboarding flow

**UI Design Considerations:**
- Role-based navigation menus
- Status indicators (badges/colors)
- Onboarding progress indicators
- Profile completion prompts

---

### 🏟️ Venue & Court Management

#### **Location Model**
**Purpose:** Physical venues/facilities containing courts

**Key UI Elements:**
- Venue selection dropdowns
- Location cards with images and amenities
- Operating hours display
- Contact information sections
- Map integration for directions

#### **Court Model**
**Purpose:** Individual playing surfaces within venues

**Key Fields for UI:**
- `sport_type`: Sport-specific icons and colors
- `surface`: Surface type indicators (clay, grass, hard)
- `is_indoor`/`has_lighting`: Amenity icons
- `status`: Availability indicators
  - `active`: Green/available
  - `maintenance`: Orange/warning
  - `inactive`: Red/unavailable

**Complex Pricing Display:**
- `time_slots`: Peak/off-peak pricing tables
- `user_type_pricing`: Member vs guest rates
- `seasonal_pricing`: Special period rates

**UI Design Patterns:**
- Court grid/list views with status indicators
- Interactive pricing calendars
- Amenity icon sets
- Booking time slot selectors

---

### 📅 Booking System

#### **Booking Model**
**Purpose:** Core reservation entity linking users, courts, and time slots

**Key Status Flow:**
```
pending → confirmed → in_progress → completed
         ↘ cancelled
```

**Essential UI Elements:**
- Booking timeline/calendar views
- Status badges with color coding
- Payment status indicators
- Participant management interfaces
- Recurring booking patterns

**Participant Management:**
- Guest vs registered user indicators
- Payment splitting interfaces
- Contact information forms
- Group booking layouts

#### **Booking Extra Model**
**Purpose:** Additional services (coaching, equipment rental)

**UI Components:**
- Add-on selection during booking
- Service pricing displays
- Quantity selectors
- Service provider information

---

### 🎓 Learning & Coaching

#### **Coach Model**
**Purpose:** Professional instructors and their services

**Profile Display Elements:**
- Coach rating and review stars
- Experience badges by sport
- Availability calendar widgets
- Pricing tier displays
- Certification/credential badges

**Availability Patterns:**
- Weekly schedule grids
- Time slot booking interfaces
- Recurring availability displays

#### **Lesson Model**
**Purpose:** Structured coaching sessions

**UI Flow Patterns:**
- Lesson type selection (private, group, clinic)
- Skill level indicators
- Progress tracking interfaces
- Booking confirmation flows

---

### 💰 Financial Management

#### **Payment Model**
**Purpose:** Transaction tracking and payment processing

**Status Indicators:**
- `pending`: Processing spinner
- `completed`: Success checkmark
- `failed`: Error indication
- `refunded`: Refund badge

**UI Components:**
- Payment method selectors
- Transaction history tables
- Receipt generation
- Refund processing interfaces

#### **Wallet Model**
**Purpose:** User account credit system

**Display Elements:**
- Balance indicators
- Top-up interfaces
- Transaction logs
- Low balance alerts

---

### 🏪 Inventory & Equipment

#### **Equipment Model**
**Purpose:** Rental equipment catalog

**Product Display:**
- Equipment cards with images
- Brand and model information
- Availability indicators
- Rental pricing
- Condition status badges

#### **Vending Machine Integration**
**Purpose:** Automated equipment dispensing

**UI Features:**
- QR code generation for rentals
- Machine location maps
- Inventory level indicators
- Return process flows

---

### 🏆 Social & Community

#### **Community Model**
**Purpose:** User groups and social features

**Community Cards:**
- Member count displays
- Sport type badges
- Privacy level indicators
- Join/leave buttons
- Activity feeds

#### **Notification Model**
**Purpose:** System and user communications

**Notification Types:**
- Booking confirmations
- Payment receipts
- Community updates
- System announcements

**UI Patterns:**
- Notification bells with counters
- In-app notification panels
- Email/SMS preference toggles
- Priority level indicators

---

## 📋 Detailed Model Schemas

### 👥 **User Model Fields**

```json
{
  "id": "string (uuid)",
  "email": "string (email format)",
  "phone": "string (international format: +919009781991)",
  "first_name": "string",
  "last_name": "string",
  "role": "enum [superadmin, manager, tenant_admin, location_manager, customer, coach, staff]",
  "status": "enum [active, inactive, blocked]",
  "tenant_id": "string (uuid) - optional for customers",
  "location_ids": "array[string] - for location managers",
  "profile": "string (profile image URL)",
  "address": "string",
  "onboarding_completed": "boolean",
  "last_login_at": "timestamp (nullable)",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

**UI Display Examples:**
- **Role Badge**: `<span class="role-badge role-customer">Customer</span>`
- **Status Indicator**: `<div class="status-dot status-active"></div>`
- **Name Display**: `${first_name} ${last_name}`

---

### 🏟️ **Location Model Fields**

```json
{
  "id": "string (uuid)",
  "tenant_id": "string (uuid)",
  "name": "string",
  "description": "string",
  "address": "string",
  "city": "string",
  "state": "string",
  "country": "string",
  "postal_code": "string",
  "phone": "string",
  "email": "string",
  "website": "string (URL)",
  "latitude": "float64",
  "longitude": "float64",
  "timezone": "string (e.g., America/New_York)",
  "operating_hours": {
    "monday": {"start": "09:00", "end": "22:00", "closed": false},
    "tuesday": {"start": "09:00", "end": "22:00", "closed": false},
    "wednesday": {"start": "09:00", "end": "22:00", "closed": false},
    "thursday": {"start": "09:00", "end": "22:00", "closed": false},
    "friday": {"start": "09:00", "end": "22:00", "closed": false},
    "saturday": {"start": "08:00", "end": "23:00", "closed": false},
    "sunday": {"start": "08:00", "end": "21:00", "closed": false}
  },
  "amenities": "array[string] - [parking, wifi, lockers, shower, cafe]",
  "images": "array[string] - image URLs",
  "is_active": "boolean",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

---

### 🎾 **Court Model Fields**

```json
{
  "id": "string (uuid)",
  "location_id": "string (uuid)",
  "tenant_id": "string (uuid)",
  "name": "string (e.g., Court 1, Center Court)",
  "description": "string",
  "sport_type": "enum [tennis, badminton, squash, pickleball, table_tennis]",
  "surface": "enum [clay, grass, hard, synthetic, concrete]",
  "is_indoor": "boolean",
  "has_lighting": "boolean",
  "max_players": "int (2 for tennis, 4 for badminton)",
  "amenities": "array[string] - [air_conditioning, sound_system, video_recording]",
  "images": "array[string] - image URLs",
  "status": "enum [active, maintenance, inactive]",
  "pricing": {
    "base_price": "float64",
    "currency": "string (USD, EUR, INR)",
    "price_per_hour": "boolean",
    "min_booking_duration": "int (minutes)",
    "max_booking_duration": "int (minutes)",
    "time_slots": [
      {
        "name": "Peak Hours",
        "start_time": "18:00",
        "end_time": "22:00",
        "days_of_week": ["monday", "tuesday", "wednesday", "thursday", "friday"],
        "price": "float64",
        "is_active": "boolean"
      }
    ]
  },
  "availability": {
    "always_available": "boolean",
    "advance_booking_days": "int (30)",
    "cancellation_hours": "int (24)"
  }
}
```

---

### 📅 **Booking Model Fields**

```json
{
  "id": "string (uuid)",
  "tenant_id": "string (uuid)",
  "location_id": "string (uuid)",
  "court_id": "string (uuid)",
  "user_id": "string (uuid) - booking owner",
  "booking_number": "string (auto-generated: BP-20250625-001)",
  "sport_type": "enum [tennis, badminton, squash, pickleball, table_tennis]",
  "match_level": "enum [beginner, intermediate, advanced, professional]",
  "start_time": "timestamp",
  "end_time": "timestamp",
  "duration": "int (minutes)",
  "status": "enum [pending, confirmed, cancelled, completed, in_progress]",
  "origin": "enum [user, manager, admin]",
  "payment_type": "enum [full, split]",
  "total_price": "float64",
  "paid_amount": "float64",
  "currency": "string",
  "notes": "string",
  "is_recurring": "boolean",
  "recurring_id": "string (uuid)",
  "participants": [
    {
      "name": "string",
      "phone": "string",
      "email": "string",
      "user_id": "string (uuid) - nullable",
      "is_guest": "boolean",
      "price": "float64",
      "paid_to": "enum [owner, court]",
      "payment_status": "enum [paid, pending, unpaid]"
    }
  ],
  "extras": [
    {
      "type": "enum [equipment_rental, coaching, food_beverage]",
      "item_id": "string (uuid)",
      "name": "string",
      "quantity": "int",
      "price": "float64"
    }
  ]
}
```

**Status Flow Visualization:**
```
pending → confirmed → in_progress → completed
         ↘ cancelled
```

---

### 🎓 **Coach Model Fields**

```json
{
  "id": "string (uuid)",
  "tenant_id": "string (uuid)",
  "location_id": "string (uuid)",
  "user_id": "string (uuid)",
  "full_name": "string",
  "phone": "string",
  "email": "string",
  "profile_image": "string (URL)",
  "description": "string (bio/about)",
  "experience": [
    {
      "sport_type": "enum [tennis, badminton, squash, pickleball]",
      "years": "int",
      "ages": "array[string] - [junior, adult, senior]"
    }
  ],
  "availability": [
    {
      "day": "enum [monday, tuesday, wednesday, thursday, friday, saturday, sunday]",
      "start_time": "string (HH:MM)",
      "end_time": "string (HH:MM)",
      "is_active": "boolean"
    }
  ],
  "pricing_rules": [
    {
      "id": "string (uuid)",
      "name": "string (e.g., Private Tennis Lesson)",
      "sport_type": "string",
      "duration": "int (minutes)",
      "price": "float64",
      "start_time": "string (HH:MM)",
      "end_time": "string (HH:MM)",
      "days": "array[string]",
      "is_default": "boolean"
    }
  ],
  "is_active": "boolean",
  "rating": "float64 (0.0 to 5.0)",
  "total_reviews": "int"
}
```

---

### 💰 **Payment Model Fields**

```json
{
  "id": "string (uuid)",
  "tenant_id": "string (uuid)",
  "user_id": "string (uuid)",
  "reference_type": "enum [booking, rental, purchase, lesson, tournament]",
  "reference_id": "string (uuid)",
  "amount": "float64",
  "currency": "string (USD, EUR, INR)",
  "payment_method": "enum [card, cash, wallet, transfer, upi]",
  "payment_gateway": "enum [stripe, razorpay, paypal] - nullable",
  "transaction_id": "string - external gateway ID",
  "status": "enum [pending, completed, failed, refunded]",
  "failure_reason": "string - nullable",
  "refund_amount": "float64",
  "refund_reason": "string",
  "metadata": "object - additional payment data",
  "created_at": "timestamp",
  "completed_at": "timestamp - nullable"
}
```

---

### 🏪 **Equipment Model Fields**

```json
{
  "id": "string (uuid)",
  "tenant_id": "string (uuid)",
  "location_id": "string (uuid)",
  "name": "string (e.g., Wilson Pro Staff Tennis Racket)",
  "brand": "string (Wilson, Yonex, HEAD)",
  "model": "string",
  "type": "enum [racket, ball, shoe, accessory, apparel]",
  "sport_type": "enum [tennis, badminton, squash, pickleball]",
  "description": "string",
  "images": "array[string] - image URLs",
  "price": "float64 (rental price)",
  "currency": "string",
  "specifications": {
    "weight": "string (e.g., 300g)",
    "grip_size": "string (e.g., 4 3/8)",
    "string_pattern": "string (e.g., 16x19)",
    "material": "string (e.g., Graphite)"
  },
  "is_active": "boolean"
}
```

---

### 🏆 **Community Model Fields**

```json
{
  "id": "string (uuid)",
  "tenant_id": "string (uuid)",
  "location_id": "string (uuid) - nullable",
  "name": "string",
  "description": "string",
  "sport_type": "enum [tennis, badminton, squash, pickleball, all] - nullable",
  "type": "enum [public, private, invite_only]",
  "creator_id": "string (uuid)",
  "member_count": "int",
  "max_members": "int - nullable",
  "rules": "array[string] - community guidelines",
  "tags": "array[string] - [competitive, casual, beginner_friendly]",
  "image": "string (URL)",
  "is_active": "boolean"
}
```

---

### 🔔 **Notification Model Fields**

```json
{
  "id": "string (uuid)",
  "user_id": "string (uuid)",
  "tenant_id": "string (uuid)",
  "type": "enum [booking_confirmation, payment_receipt, reminder, announcement, social]",
  "title": "string",
  "message": "string",
  "data": "object - additional notification data",
  "channels": "array[string] - [push, email, sms, in_app]",
  "priority": "enum [low, normal, high, urgent]",
  "status": "enum [pending, sent, delivered, failed]",
  "is_read": "boolean",
  "scheduled_at": "timestamp - nullable",
  "sent_at": "timestamp - nullable",
  "read_at": "timestamp - nullable"
}
```

---

### 🔐 **Authentication Models**

#### **OTP Session**
```json
{
  "id": "string (uuid)",
  "identifier": "string (phone/email)",
  "method": "enum [mobile, email]",
  "otp_code": "string (6-digit)",
  "is_coach": "boolean",
  "is_verified": "boolean",
  "expires_at": "timestamp",
  "created_at": "timestamp"
}
```

#### **Session**
```json
{
  "id": "string (uuid)",
  "user_id": "string (uuid)",
  "device_info": "string",
  "ip_address": "string",
  "expires_at": "timestamp",
  "is_active": "boolean"
}
```

---

## 🎨 UI Component Mapping

### **Form Field Types by Model Field**

```typescript
// String fields
"name" | "email" | "phone" → <TextInput />
"description" | "notes" → <TextArea />

// Enum fields  
"status" | "role" | "sport_type" → <Select /> or <RadioGroup />

// Boolean fields
"is_active" | "has_lighting" → <Switch /> or <Checkbox />

// Number fields
"price" | "duration" → <NumberInput />
"rating" → <StarRating />

// Date/Time fields
"start_time" | "end_time" → <DateTimePicker />
"created_at" → <DateDisplay />

// Array fields
"images" | "amenities" → <MultiSelect /> or <TagInput />

// Complex objects
"pricing" | "availability" → <NestedForm /> components
```

### **Display Components by Data Type**

```typescript
// Status indicators
status: "active" → <Badge variant="success">Active</Badge>
status: "inactive" → <Badge variant="warning">Inactive</Badge>

// Currency display
price: 50.00, currency: "USD" → <CurrencyDisplay>$50.00</CurrencyDisplay>

// Time display
start_time, end_time → <TimeRange>2:00 PM - 4:00 PM</TimeRange>

// Rating display
rating: 4.5 → <StarRating value={4.5} readonly />

// Progress indicators
booking.status → <BookingStatusFlow currentStatus="confirmed" />
```

---

## 🎨 Design System Recommendations

### **Color Coding Standards**

#### Status Colors:
- **Green**: Active, confirmed, completed, available
- **Orange**: Pending, maintenance, warning
- **Red**: Cancelled, failed, blocked, unavailable
- **Blue**: Information, in-progress, notifications
- **Purple**: Premium features, VIP status

#### Sport Type Colors:
- **Tennis**: #FF6B35 (Orange)
- **Badminton**: #4ECDC4 (Teal)
- **Squash**: #45B7D1 (Blue)
- **Pickleball**: #96CEB4 (Green)
- **Table Tennis**: #FFEAA7 (Yellow)

### **Icon System**

#### Court Types:
- 🎾 Tennis
- 🏸 Badminton
- 🔷 Squash
- 🏓 Table Tennis
- ⚡ Indoor courts
- ☀️ Outdoor courts

#### User Roles:
- 👑 Superadmin
- 🏢 Manager
- 🏟️ Tenant Admin
- 📍 Location Manager
- 👤 Customer
- 🎯 Coach
- 👔 Staff

---

## 📱 Mobile-First Considerations

### **Responsive Breakpoints**
- **Mobile**: 320px - 768px
- **Tablet**: 768px - 1024px
- **Desktop**: 1024px+

### **Mobile-Optimized Components**

#### Booking Flow:
1. **Court Selection**: Swipeable court cards
2. **Time Selection**: Touch-friendly time slots
3. **Payment**: Mobile payment integration
4. **Confirmation**: QR codes for easy access

#### Coach Booking:
1. **Coach Discovery**: Filter-based search
2. **Availability**: Calendar picker
3. **Lesson Details**: Collapsible sections
4. **Payment**: Simplified checkout

---

## 🔄 User Journey Flows

### **Customer Booking Journey**
```
Login → Location Select → Court Browse → Time Select → 
Extras Add → Payment → Confirmation → Check-in
```

### **Coach Management Journey**
```
Login → Dashboard → Schedule View → Lesson Management → 
Student Progress → Earnings → Profile Update
```

### **Admin Management Journey**
```
Login → Overview Dashboard → Court Management → 
Booking Analytics → Financial Reports → User Management
```

---

## 📊 Data Visualization Needs

### **Dashboard Widgets**

#### For Admins:
- Revenue charts (daily/weekly/monthly)
- Court utilization heatmaps
- Booking status pie charts
- User growth line graphs

#### For Coaches:
- Lesson completion rates
- Student progress tracking
- Earnings over time
- Schedule occupancy

#### For Customers:
- Booking history timeline
- Spending analytics
- Playing frequency charts
- Achievement badges

---

## 🎯 Key UI/UX Patterns

### **Booking Interfaces**
- **Calendar View**: Month/week/day switching
- **Time Slots**: Visual availability grids
- **Multi-step Forms**: Progress indicators
- **Confirmation**: Clear summary displays

### **Payment Flows**
- **Method Selection**: Visual payment options
- **Split Payments**: Participant contribution tracking
- **Receipts**: Downloadable/shareable formats
- **Refunds**: Clear process explanation

### **Social Features**
- **Community Cards**: Engaging community previews
- **Activity Feeds**: Real-time updates
- **Messaging**: In-app communication
- **Reviews**: Star ratings and testimonials

---

## 🚀 Technical Integration Points

### **Real-time Features**
- Live court availability updates
- Instant booking confirmations
- Real-time payment processing
- Push notifications

### **External Integrations**
- **Maps**: Location and directions
- **Payment**: Stripe integration
- **Messaging**: SMS/Email notifications
- **QR Codes**: Equipment and access

---

## 💡 Design Best Practices

### **Accessibility**
- High contrast color schemes
- Large touch targets (44px minimum)
- Screen reader compatible
- Keyboard navigation support

### **Performance**
- Lazy loading for images
- Progressive web app features
- Offline booking capabilities
- Fast loading animations

### **User Experience**
- Clear error messages
- Intuitive navigation
- Consistent design patterns
- Helpful onboarding flows

---

## 📋 Component Library Suggestions

### **Core Components**
- `BookingCard`: Unified booking display
- `CourtGrid`: Court selection interface
- `PaymentWidget`: Payment processing
- `SchedulePicker`: Time selection
- `UserProfile`: User information display
- `StatusBadge`: Universal status indicators

### **Layout Components**
- `DashboardLayout`: Role-based dashboards
- `BookingFlow`: Multi-step booking process
- `AdminPanel`: Management interfaces
- `MobileNav`: Mobile navigation patterns
