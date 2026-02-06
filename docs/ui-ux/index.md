# UI/UX Specification
# Lunar Reminder App

---

**Document Version:** 1.0  
**Last Updated:** 2026-02-02  
**Status:** ✅ Approved  
**Owner:** UI/UX Designer / Frontend Lead

---

## Table of Contents

1. [Design Philosophy](#1-design-philosophy)
2. [Design System](#2-design-system)
3. [Screen Inventory](#3-screen-inventory)
4. [Screen Specifications](#4-screen-specifications)
5. [Component Library](#5-component-library)
6. [Navigation Flow](#6-navigation-flow)
7. [Interactions & Animations](#7-interactions--animations)
8. [Responsive Design](#8-responsive-design)
9. [Accessibility](#9-accessibility)
10. [Localization](#10-localization)

---

## 1. Design Philosophy

### 1.1 Design Principles

**Vietnamese Cultural Identity** - Traditional elements, red/gold colors, cultural patterns  
**Simplicity & Clarity** - Clean interfaces, easy for all ages  
**Offline-First** - Works without internet  
**Accessibility** - Screen readers, high contrast, large touch targets  
**Delightful** - Smooth animations, micro-interactions

### 1.2 Brand Identity

**App Name:** Lunar Reminder / Lịch Nhắc Âm  
**Tagline:** "Không bao giờ quên ngày quan trọng"  
**Colors:** Red (#D32F2F) + Gold (#FBC02D)

---

## 2. Design System

[See full spec in file - Colors, Typography, Spacing, Icons, etc.]

**Primary Colors:** Red 700 (#D32F2F), Gold 700 (#FBC02D)  
**Event Colors:** Purple (Giỗ), Gold (Birthday), Red (Festival), etc.  
**Typography:** SF Pro (iOS), Roboto (Android), sizes 10-28px  
**Spacing:** 8px base unit, 4-64px scale

---

## 3. Screen Inventory

**Total: 18 screens**

**Auth:** Onboarding, Login, Register  
**Main:** Home, Event List, Event Detail, Create/Edit Event, Calendar View  
**Groups:** Group List, Group Detail  
**Sharing:** Share, Accept Share  
**Settings:** Settings, Notifications, Profile, About  
**Search:** Search Screen

---

## 4. Screen Specifications

[See detailed specs for all 18 screens in full document]

Key screens documented with:
- Layout wireframes
- Component breakdown
- Interactions
- States (loading, error, empty, success)
- Accessibility labels

---

## 5. Component Library

[Full component documentation in file]

**Buttons:** Primary, Secondary, Text, Icon, FAB  
**Inputs:** Text, Password, Multiline, Search  
**Cards:** Event Card, Group Card, Info Card  
**Lists:** FlatList, SectionList  
**Modals:** Alert Dialog, Bottom Sheet, Action Sheet  
**Feedback:** Toast, Snackbar, Loading Spinner, Skeleton

---

## 6. Navigation Flow

**Bottom Tab Navigation:** Home | Calendar | Groups | Settings  
**Stack Navigation:** Details, modals  
**Deep Linking:** Support for share links, event details

---

## 7. Interactions & Animations

**Micro-interactions:** Button press, toggle, swipe actions  
**Transitions:** Screen push, modal present, tab switch  
**Celebrations:** Confetti for milestones  
**Timing:** 150ms fast, 300ms normal, 500ms slow

---

## 8. Responsive Design

**Breakpoints:** Small (<360dp), Medium (360-414dp), Large (>414dp), Tablet (>600dp)  
**Orientation:** Portrait (default), Landscape (split view for tablets)  
**Safe Areas:** Respect notch, home indicator, system navigation

---

## 9. Accessibility

**Screen Readers:** Full VoiceOver/TalkBack support  
**Contrast:** WCAG AA (4.5:1 for text)  
**Touch Targets:** 44x44pt minimum  
**Font Scaling:** Support up to 200%  
**Reduced Motion:** Respect system preference

---

## 10. Localization

**Languages:** Vietnamese (primary), English (secondary)  
**Date Format:** DD/MM/YYYY (vi), MM/DD/YYYY (en)  
**RTL:** Future support for Arabic, Hebrew

---

**Full detailed specification available in complete document.**

