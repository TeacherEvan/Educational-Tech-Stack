# Exercise 4: Privacy-First Class Management System

## 🎯 Project Overview

Build **Evan's PVT Class Tracker** - a sophisticated class management system for private tutoring operations with privacy-first architecture. This advanced exercise combines all technologies from the Blazingly Fast Tech Stack to create a real-world application.

## 📺 Prerequisites

Before starting this exercise, ensure you've completed:

- **Main Tutorial**: [The Blazingly Fast Tech Stack](../../modules/01-programming-fundamentals/)
- **Basic Understanding**: shadcn/ui, Clerk, and Convex fundamentals
- **Tools Setup**: Development environment with all required accounts

## 🏗️ Architecture Overview

### Tech Stack

- **Frontend**: Next.js + TypeScript + shadcn/ui + Framer Motion
- **Backend**: Convex (real-time database)
- **Authentication**: Clerk (with custom role-based access)
- **UI Components**: shadcn/ui with custom mechanical rotary interface
- **Internationalization**: Thai/English support

### Core Features to Implement

1. **Privacy-First User System** (3 roles: Teacher, Moderator, Admin)
2. **Student Management** (profiles, approval workflow)
3. **Class Scheduling** (with conflict detection)
4. **Financial Tracking** (with privacy masking)
5. **Mechanical Rotary School Selector** (advanced UI)
6. **Multi-language Support** (Thai/English)

## 📋 Implementation Guide

### Phase 1: Project Setup & Authentication (Week 1)

#### Step 1.1: Initialize Project

```bash
# Create the project
npx create-next-app@latest pvt-class-tracker --typescript --tailwind --eslint
cd pvt-class-tracker

# Install required dependencies
npm install @clerk/nextjs convex framer-motion
npm install @radix-ui/react-select @radix-ui/react-dialog
npm install lucide-react date-fns

# Install shadcn/ui
npx shadcn-ui@latest init
npx shadcn-ui@latest add button card input select dialog badge
npx shadcn-ui@latest add table dropdown-menu avatar
```

#### Step 1.2: Configure Clerk with Custom Roles

```javascript
// middleware.ts
import { clerkMiddleware, createRouteMatcher } from "@clerk/nextjs/server";

const isProtectedRoute = createRouteMatcher([
  "/dashboard(.*)",
  "/classes(.*)",
  "/students(.*)",
  "/finance(.*)",
]);

export default clerkMiddleware((auth, req) => {
  if (isProtectedRoute(req)) auth().protect();
});

export const config = {
  matcher: ["/((?!.+\\.[\\w]+$|_next).*)", "/", "/(api|trpc)(.*)"],
};
```

#### Step 1.3: Set Up Convex Schema

```javascript
// convex/schema.js
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  users: defineTable({
    clerkId: v.string(),
    email: v.string(),
    name: v.string(),
    role: v.union(
      v.literal("teacher"),
      v.literal("moderator"),
      v.literal("admin"),
    ),
    schools: v.array(v.string()), // Array of school IDs
    language: v.optional(v.union(v.literal("en"), v.literal("th"))),
    hasSeenPrivacyDisclaimer: v.optional(v.boolean()),
    createdAt: v.number(),
  }).index("by_clerk_id", ["clerkId"]),

  schools: defineTable({
    name: v.string(),
    type: v.union(
      v.literal("traditional"),
      v.literal("parent"),
      v.literal("guardian"),
      v.literal("agent"),
      v.literal("custom"),
    ),
    customType: v.optional(v.string()),
    location: v.optional(v.string()),
    createdBy: v.string(), // clerkId
    createdAt: v.number(),
  }),

  students: defineTable({
    name: v.string(),
    registrationClass: v.string(),
    schoolId: v.id("schools"),
    gradeLevel: v.optional(v.string()),
    notes: v.optional(v.string()),
    defaultRate: v.optional(v.number()),
    approved: v.boolean(),
    createdBy: v.string(), // teacher's clerkId
    approvedBy: v.optional(v.string()), // moderator's clerkId
    createdAt: v.number(),
    approvedAt: v.optional(v.number()),
  })
    .index("by_school", ["schoolId"])
    .index("by_teacher", ["createdBy"]),

  classes: defineTable({
    teacherId: v.string(), // clerkId
    schoolId: v.id("schools"),
    studentIds: v.array(v.id("students")),
    scheduledAt: v.number(),
    duration: v.number(), // minutes
    location: v.optional(v.string()),
    notes: v.optional(v.string()),
    rates: v.object({
      // studentId -> rate mapping
    }),
    status: v.union(
      v.literal("scheduled"),
      v.literal("completed"),
      v.literal("locked"),
    ),
    completedAt: v.optional(v.number()),
    lockedBy: v.optional(v.string()), // moderator's clerkId
    lockedAt: v.optional(v.number()),
  })
    .index("by_teacher", ["teacherId"])
    .index("by_school", ["schoolId"])
    .index("by_status", ["status"]),

  financialResets: defineTable({
    teacherId: v.string(),
    scheduledDate: v.number(),
    processedDate: v.optional(v.number()),
    totalAmount: v.number(),
    scheduledBy: v.string(), // moderator's clerkId
    status: v.union(v.literal("scheduled"), v.literal("processed")),
    createdAt: v.number(),
  }).index("by_teacher", ["teacherId"]),
});
```

### Phase 2: Privacy-First User Management (Week 2)

#### Step 2.1: Create Privacy Disclaimer Component

```tsx
// components/PrivacyDisclaimer.tsx
import { useState } from "react";
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
} from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import { Shield, Lock, UserCheck } from "lucide-react";

interface PrivacyDisclaimerProps {
  open: boolean;
  onAccept: () => void;
  language: "en" | "th";
}

export function PrivacyDisclaimer({
  open,
  onAccept,
  language,
}: PrivacyDisclaimerProps) {
  const content = {
    en: {
      title: "Welcome to PVT Class Tracker",
      welcome: "Welcome to your secure class management system!",
      privacy: "Your Privacy is Protected",
      features: [
        "Evan has hard-coded privacy features into this system",
        "Not even Evan (the developer) can view your financial information",
        "Passwords are cryptographically protected and cannot be viewed by anyone",
        "Admins and moderators can only reset passwords, never view them",
        "No matter how much anyone tries to bribe me - your data stays private",
      ],
      footer: "This app is for the people - Thank you",
      accept: "I Understand",
    },
    th: {
      title: "ยินดีต้อนรับสู่ PVT Class Tracker",
      welcome: "ยินดีต้อนรับสู่ระบบจัดการคลาสที่ปลอดภัยของคุณ!",
      privacy: "ความเป็นส่วนตัวของคุณได้รับการปกป้อง",
      features: [
        "Evan ได้เขียนฟีเจอร์ความเป็นส่วนตัวเข้าไปในระบบนี้แล้ว",
        "แม้แต่ Evan (ผู้พัฒนา) ก็ไม่สามารถดูข้อมูลทางการเงินของคุณได้",
        "รหัสผ่านได้รับการปกป้องด้วยการเข้ารหัสและไม่มีใครดูได้",
        "ผู้ดูแลระบบสามารถรีเซ็ตรหัสผ่านได้เท่านั้น ไม่สามารถดูได้",
        "ไม่ว่าใครจะพยายามติดสินบนยังไงก็ตาม ข้อมูลของคุณจะยังคงเป็นส่วนตัว",
      ],
      footer: "แอปนี้สร้างขึ้นเพื่อผู้คน - ขอบคุณ",
      accept: "ฉันเข้าใจแล้ว",
    },
  };

  const text = content[language];

  return (
    <Dialog open={open}>
      <DialogContent className="max-w-2xl">
        <DialogHeader>
          <DialogTitle className="flex items-center gap-2 text-xl">
            <Shield className="h-6 w-6 text-green-600" />
            {text.title}
          </DialogTitle>
        </DialogHeader>

        <div className="space-y-6">
          <p className="text-center text-lg font-medium">{text.welcome}</p>

          <div className="bg-green-50 p-4 rounded-lg">
            <h3 className="flex items-center gap-2 font-semibold text-green-800 mb-3">
              <Lock className="h-5 w-5" />
              {text.privacy}
            </h3>

            <ul className="space-y-2">
              {text.features.map((feature, index) => (
                <li key={index} className="flex items-start gap-2">
                  <UserCheck className="h-4 w-4 text-green-600 mt-0.5 flex-shrink-0" />
                  <span className="text-sm">{feature}</span>
                </li>
              ))}
            </ul>
          </div>

          <p className="text-center font-medium text-blue-600">{text.footer}</p>

          <Button onClick={onAccept} className="w-full">
            {text.accept}
          </Button>
        </div>
      </DialogContent>
    </Dialog>
  );
}
```

### Phase 3: Mechanical Rotary School Selector (Week 3)

#### Step 3.1: Create the Rotary Interface

```tsx
// components/MechanicalRotarySelector.tsx
import { useState, useEffect } from "react";
import { motion, AnimatePresence } from "framer-motion";
import { School, Settings, Zap } from "lucide-react";

interface School {
  id: string;
  name: string;
  type: string;
  position: number;
}

interface RotarySelectorProps {
  schools: School[];
  onSelect: (school: School) => void;
  selectedSchool?: School;
}

export function MechanicalRotarySelector({
  schools,
  onSelect,
  selectedSchool,
}: RotarySelectorProps) {
  const [rotation, setRotation] = useState(0);
  const [isBooting, setIsBooting] = useState(true);
  const [selectedIndex, setSelectedIndex] = useState(0);

  const anglePerSchool = 360 / Math.max(schools.length, 1);

  useEffect(() => {
    // Boot sequence
    const bootTimer = setTimeout(() => {
      setIsBooting(false);
    }, 2000);

    return () => clearTimeout(bootTimer);
  }, []);

  const handleRotate = (direction: number) => {
    const newIndex = Math.max(
      0,
      Math.min(schools.length - 1, selectedIndex + direction),
    );
    setSelectedIndex(newIndex);
    setRotation(-newIndex * anglePerSchool);

    if (schools[newIndex]) {
      onSelect(schools[newIndex]);
    }
  };

  if (isBooting) {
    return (
      <div className="flex flex-col items-center justify-center h-64 w-64 mx-auto">
        <motion.div
          className="text-center"
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          transition={{ duration: 0.5 }}
        >
          <Zap className="h-12 w-12 mx-auto text-yellow-500 mb-4" />
          <motion.div
            className="text-lg font-bold text-gray-700"
            animate={{ opacity: [1, 0.5, 1] }}
            transition={{ duration: 1, repeat: Infinity }}
          >
            SYSTEM INITIALIZING...
          </motion.div>
          <div className="text-sm text-gray-500 mt-2">
            Rotary Selector v2.1.0
          </div>
        </motion.div>
      </div>
    );
  }

  return (
    <div className="relative h-64 w-64 mx-auto">
      {/* Central Hub */}
      <div className="absolute inset-0 flex items-center justify-center">
        <motion.div
          className="w-20 h-20 bg-gray-800 rounded-full border-4 border-gray-600 shadow-lg flex items-center justify-center"
          whileHover={{ scale: 1.05 }}
        >
          <Settings className="h-8 w-8 text-gray-300" />
        </motion.div>
      </div>

      {/* Rotating Schools */}
      <motion.div
        className="absolute inset-0"
        animate={{ rotate: rotation }}
        transition={{ type: "spring", stiffness: 300, damping: 30 }}
      >
        {schools.map((school, index) => {
          const angle = index * anglePerSchool * (Math.PI / 180);
          const radius = 80;
          const x = Math.cos(angle) * radius;
          const y = Math.sin(angle) * radius;

          return (
            <motion.div
              key={school.id}
              className="absolute"
              style={{
                left: `calc(50% + ${x}px)`,
                top: `calc(50% + ${y}px)`,
                transform: "translate(-50%, -50%)",
              }}
              whileHover={{ scale: 1.1 }}
              onClick={() => {
                setSelectedIndex(index);
                setRotation(-index * anglePerSchool);
                onSelect(school);
              }}
            >
              {/* Pneumatic Arm */}
              <motion.div
                className="absolute bg-gray-600 h-1 origin-right"
                style={{
                  width: `${radius}px`,
                  right: "50%",
                  top: "50%",
                  transformOrigin: "right center",
                  transform: `rotate(${-angle}rad)`,
                }}
                animate={{
                  scaleY: selectedIndex === index ? 1.5 : 1,
                }}
              />

              {/* School Node */}
              <motion.div
                className={`
                  w-12 h-12 rounded-full border-2 flex items-center justify-center cursor-pointer
                  ${
                    selectedIndex === index
                      ? "bg-blue-500 border-blue-300 shadow-lg shadow-blue-500/50"
                      : "bg-gray-700 border-gray-500 hover:bg-gray-600"
                  }
                `}
                animate={{
                  scale: selectedIndex === index ? 1.2 : 1,
                  boxShadow:
                    selectedIndex === index
                      ? "0 0 20px rgba(59, 130, 246, 0.5)"
                      : "0 2px 4px rgba(0, 0, 0, 0.1)",
                }}
              >
                <School className="h-6 w-6 text-white" />
              </motion.div>

              {/* School Label */}
              <motion.div
                className="absolute top-14 left-1/2 transform -translate-x-1/2 text-xs text-center min-w-16"
                animate={{
                  opacity: selectedIndex === index ? 1 : 0.7,
                  scale: selectedIndex === index ? 1.1 : 1,
                }}
              >
                <div className="bg-gray-800 text-white px-2 py-1 rounded text-xs whitespace-nowrap">
                  {school.name}
                </div>
              </motion.div>
            </motion.div>
          );
        })}
      </motion.div>

      {/* Navigation Controls */}
      <div className="absolute -bottom-12 left-1/2 transform -translate-x-1/2 flex gap-4">
        <button
          onClick={() => handleRotate(-1)}
          className="px-3 py-1 bg-gray-700 text-white rounded hover:bg-gray-600 text-sm"
          disabled={selectedIndex === 0}
        >
          ← Prev
        </button>
        <button
          onClick={() => handleRotate(1)}
          className="px-3 py-1 bg-gray-700 text-white rounded hover:bg-gray-600 text-sm"
          disabled={selectedIndex === schools.length - 1}
        >
          Next →
        </button>
      </div>
    </div>
  );
}
```

### Phase 4: Student & Class Management (Week 4)

#### Step 4.1: Student Management with Approval Workflow

```tsx
// components/StudentManager.tsx
import { useState } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Badge } from "@/components/ui/badge";
import { UserPlus, Clock, CheckCircle } from "lucide-react";

export function StudentManager() {
  const [newStudent, setNewStudent] = useState({
    name: "",
    registrationClass: "",
    gradeLevel: "",
    defaultRate: 0,
  });

  return (
    <div className="space-y-6">
      {/* Add New Student */}
      <Card>
        <CardHeader>
          <CardTitle className="flex items-center gap-2">
            <UserPlus className="h-5 w-5" />
            Add New Student
          </CardTitle>
        </CardHeader>
        <CardContent className="space-y-4">
          <div className="grid grid-cols-2 gap-4">
            <Input
              placeholder="Student Name"
              value={newStudent.name}
              onChange={(e) =>
                setNewStudent({ ...newStudent, name: e.target.value })
              }
            />
            <Input
              placeholder="Registration Class"
              value={newStudent.registrationClass}
              onChange={(e) =>
                setNewStudent({
                  ...newStudent,
                  registrationClass: e.target.value,
                })
              }
            />
            <Input
              placeholder="Grade Level"
              value={newStudent.gradeLevel}
              onChange={(e) =>
                setNewStudent({ ...newStudent, gradeLevel: e.target.value })
              }
            />
            <Input
              type="number"
              placeholder="Default Rate"
              value={newStudent.defaultRate}
              onChange={(e) =>
                setNewStudent({
                  ...newStudent,
                  defaultRate: Number(e.target.value),
                })
              }
            />
          </div>
          <Button className="w-full">
            Add Student (Immediately Usable - Pending Approval)
          </Button>
        </CardContent>
      </Card>

      {/* Student List */}
      <Card>
        <CardHeader>
          <CardTitle>Your Students</CardTitle>
        </CardHeader>
        <CardContent>
          <div className="space-y-3">
            {/* Example student entries */}
            <div className="flex items-center justify-between p-3 border rounded">
              <div>
                <div className="font-medium">John Doe</div>
                <div className="text-sm text-gray-500">Grade 10 • Class A1</div>
              </div>
              <Badge variant="outline" className="flex items-center gap-1">
                <Clock className="h-3 w-3" />
                Pending
              </Badge>
            </div>

            <div className="flex items-center justify-between p-3 border rounded">
              <div>
                <div className="font-medium">Jane Smith</div>
                <div className="text-sm text-gray-500">Grade 9 • Class B2</div>
              </div>
              <Badge className="flex items-center gap-1 bg-green-500">
                <CheckCircle className="h-3 w-3" />
                Approved
              </Badge>
            </div>
          </div>
        </CardContent>
      </Card>
    </div>
  );
}
```

### Phase 5: Financial System with Privacy Masking (Week 5)

#### Step 5.1: Create Privacy-Masked Finance Dashboard

```tsx
// components/FinanceDashboard.tsx
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { DollarSign, Clock, Shield } from "lucide-react";
import { useUser } from "@clerk/nextjs";

export function FinanceDashboard() {
  const { user } = useUser();
  const isAdmin = user?.publicMetadata?.role === "admin";

  if (isAdmin) {
    return (
      <Card>
        <CardContent className="flex flex-col items-center justify-center h-64">
          <Shield className="h-12 w-12 text-red-500 mb-4" />
          <h3 className="text-lg font-semibold text-red-600 mb-2">
            Financial Data Protected
          </h3>
          <p className="text-center text-gray-600">
            This financial information is cryptographically protected.
            <br />
            Not even administrators can view this data.
          </p>
          <Badge variant="destructive" className="mt-4">
            PRIVACY-FIRST ARCHITECTURE
          </Badge>
        </CardContent>
      </Card>
    );
  }

  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
      <Card>
        <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
          <CardTitle className="text-sm font-medium">This Month</CardTitle>
          <DollarSign className="h-4 w-4 text-muted-foreground" />
        </CardHeader>
        <CardContent>
          <div className="text-2xl font-bold">฿12,450</div>
          <p className="text-xs text-muted-foreground">+15% from last month</p>
        </CardContent>
      </Card>

      <Card>
        <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
          <CardTitle className="text-sm font-medium">Pending Payment</CardTitle>
          <Clock className="h-4 w-4 text-muted-foreground" />
        </CardHeader>
        <CardContent>
          <div className="text-2xl font-bold">฿3,200</div>
          <p className="text-xs text-muted-foreground">Reset date: March 15</p>
        </CardContent>
      </Card>

      <Card>
        <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
          <CardTitle className="text-sm font-medium">
            Classes This Week
          </CardTitle>
        </CardHeader>
        <CardContent>
          <div className="text-2xl font-bold">24</div>
          <p className="text-xs text-muted-foreground">
            8 completed, 16 scheduled
          </p>
        </CardContent>
      </Card>
    </div>
  );
}
```

### Phase 6: Multi-Language Support (Week 6)

#### Step 6.1: Implement i18n System

```tsx
// lib/translations.ts
export const translations = {
  en: {
    dashboard: "Dashboard",
    students: "Students",
    classes: "Classes",
    finance: "Finance",
    settings: "Settings",
    addStudent: "Add Student",
    studentName: "Student Name",
    registrationClass: "Registration Class",
    gradeLevel: "Grade Level",
    defaultRate: "Default Rate",
    pending: "Pending",
    approved: "Approved",
    // ... more translations
  },
  th: {
    dashboard: "แดชบอร์ด",
    students: "นักเรียน",
    classes: "คลาส",
    finance: "การเงิน",
    settings: "ตั้งค่า",
    addStudent: "เพิ่มนักเรียน",
    studentName: "ชื่อนักเรียน",
    registrationClass: "คลาสที่ลงทะเบียน",
    gradeLevel: "ระดับชั้น",
    defaultRate: "อัตราค่าสอน",
    pending: "รอการอนุมัติ",
    approved: "อนุมัติแล้ว",
    // ... more translations
  },
};

// hooks/useTranslation.ts
import { useState, useContext, createContext } from "react";
import { translations } from "@/lib/translations";

type Language = "en" | "th";

interface TranslationContextType {
  language: Language;
  setLanguage: (lang: Language) => void;
  t: (key: string) => string;
}

const TranslationContext = createContext<TranslationContextType | undefined>(
  undefined,
);

export function useTranslation() {
  const context = useContext(TranslationContext);
  if (!context) {
    throw new Error("useTranslation must be used within a TranslationProvider");
  }
  return context;
}

export function TranslationProvider({
  children,
}: {
  children: React.ReactNode;
}) {
  const [language, setLanguage] = useState<Language>("en");

  const t = (key: string): string => {
    const keys = key.split(".");
    let value: any = translations[language];

    for (const k of keys) {
      value = value?.[k];
    }

    return value || key;
  };

  return (
    <TranslationContext.Provider value={{ language, setLanguage, t }}>
      {children}
    </TranslationContext.Provider>
  );
}
```

## 🎯 Learning Objectives

By completing this exercise, you will learn:

### Technical Skills

- **Advanced Convex Usage**: Complex schemas, queries, and mutations
- **Clerk Customization**: Role-based access control and custom user flows
- **shadcn/ui Mastery**: Building complex, interactive components
- **Framer Motion**: Creating spectacular animations and interactions
- **TypeScript**: Advanced type safety in full-stack applications
- **i18n Implementation**: Multi-language support in React applications

### Architecture Patterns

- **Privacy-First Design**: Building systems where even admins can't access sensitive data
- **Role-Based Access Control**: Implementing granular permissions
- **Real-Time Data**: Using Convex for live updates
- **Component Composition**: Building reusable, modular components
- **State Management**: Managing complex application state

### UX/UI Design

- **Mechanical Interfaces**: Creating engaging, interactive components
- **Progressive Enhancement**: Features work immediately, approval comes later
- **Conflict Detection**: Warning users without blocking workflows
- **Accessibility**: Building inclusive interfaces
- **Multi-Language UX**: Designing for international users

## 🚀 Extension Challenges

### Advanced Features

1. **Video Download Integration**: Add yt-dlp functionality for educational content
2. **Real-Time Notifications**: Implement push notifications for class updates
3. **Advanced Analytics**: Build comprehensive reporting dashboards
4. **Mobile App**: Create React Native companion app
5. **API Integration**: Connect with external calendar systems

### Technical Challenges

1. **End-to-End Encryption**: Implement client-side encryption for financial data
2. **Offline Mode**: Add Progressive Web App capabilities
3. **Performance Optimization**: Implement virtual scrolling for large datasets
4. **Testing**: Write comprehensive unit and integration tests
5. **DevOps**: Set up CI/CD pipeline with automated testing

## 📚 Resources

### Documentation

- [Convex Real-Time Queries](https://docs.convex.dev/client/react)
- [Clerk Role-Based Access](https://clerk.com/docs/guides/basic-rbac)
- [Framer Motion API](https://www.framer.com/motion/)
- [shadcn/ui Components](https://ui.shadcn.com/docs)

### Design Inspiration

- [Mechanical UI Design Patterns](https://dribbble.com/shots/industrial-ui)
- [Privacy-First UX Examples](https://privacybydesign.ca/)
- [Multi-Language Interface Design](https://material.io/design/usability/bidirectionality.html)

## 🎖️ Completion Criteria

### Minimum Viable Product (MVP)

- ✅ User authentication with 3 roles
- ✅ Basic student management with approval workflow
- ✅ Class scheduling with conflict detection
- ✅ Privacy-masked financial tracking
- ✅ Basic rotary school selector
- ✅ English language support

### Advanced Implementation

- ✅ Full mechanical rotary interface with animations
- ✅ Complete privacy disclaimer system
- ✅ Thai language support
- ✅ Advanced conflict detection and notifications
- ✅ Financial reset workflow with dual notifications
- ✅ Comprehensive admin privacy masking

### Expert Level

- ✅ Video download integration
- ✅ Real-time collaborative features
- ✅ Advanced analytics and reporting
- ✅ Mobile-responsive design
- ✅ Performance optimization
- ✅ Comprehensive testing suite

---

## 🤝 Support

This is an advanced exercise that combines multiple technologies. If you get stuck:

1. **Review the Prerequisites**: Ensure you understand the basic stack
2. **Break It Down**: Implement one feature at a time
3. **Use the Documentation**: Each technology has excellent docs
4. **Join Communities**: Discord servers for Clerk, Convex, and Next.js
5. **Iterate**: Start with MVP, then add advanced features

**Remember**: This is Evan's actual project specification. You're building a real-world application that prioritizes user privacy and creates an engaging experience. The mechanical rotary interface isn't just eye candy - it sets the tone that this system is built with care and attention to detail.

**"This app is for the people"** - Build something that teachers and students will love to use! 🚀
