# Phases 5–9 — Atoms, Molecules, Theme, Showcase, Barrels

For each atom, create the component, its `index.ts` barrel, and (where shown) supporting files. Copy the sources below exactly.

## Phase 5 — Generate Atoms with Full Examples

### Atom: Button

**`src/components/atoms/Button/Button.tsx`**

```typescript
import React from 'react';
import {
  TouchableOpacity,
  Text,
  ActivityIndicator,
  StyleSheet,
  ViewStyle,
  TextStyle,
} from 'react-native';
import { colors } from '../../../theme/colors';
import { spacing } from '../../../theme/spacing';
import { typography } from '../../../theme/typography';

export type ButtonVariant =
  | 'primary'
  | 'secondary'
  | 'outline'
  | 'ghost'
  | 'danger';
export type ButtonSize = 'sm' | 'md' | 'lg';

interface ButtonProps {
  title: string;
  onPress: () => void;
  variant?: ButtonVariant;
  size?: ButtonSize;
  disabled?: boolean;
  loading?: boolean;
  style?: ViewStyle;
  textStyle?: TextStyle;
}

const variantStyles: Record<
  ButtonVariant,
  { container: ViewStyle; text: TextStyle }
> = {
  primary: {
    container: { backgroundColor: colors.primary },
    text: { color: colors.white },
  },
  secondary: {
    container: { backgroundColor: colors.secondary },
    text: { color: colors.white },
  },
  outline: {
    container: {
      backgroundColor: 'transparent',
      borderWidth: 1.5,
      borderColor: colors.primary,
    },
    text: { color: colors.primary },
  },
  ghost: {
    container: { backgroundColor: 'transparent' },
    text: { color: colors.primary },
  },
  danger: {
    container: { backgroundColor: colors.danger },
    text: { color: colors.white },
  },
};

const sizeStyles: Record<
  ButtonSize,
  { container: ViewStyle; text: TextStyle }
> = {
  sm: {
    container: { paddingVertical: spacing.xs, paddingHorizontal: spacing.sm },
    text: { fontSize: typography.sm },
  },
  md: {
    container: { paddingVertical: spacing.sm, paddingHorizontal: spacing.md },
    text: { fontSize: typography.md },
  },
  lg: {
    container: { paddingVertical: spacing.md, paddingHorizontal: spacing.lg },
    text: { fontSize: typography.lg },
  },
};

const Button: React.FC<ButtonProps> = ({
  title,
  onPress,
  variant = 'primary',
  size = 'md',
  disabled = false,
  loading = false,
  style,
  textStyle,
}) => {
  const v = variantStyles[variant];
  const s = sizeStyles[size];
  return (
    <TouchableOpacity
      onPress={onPress}
      disabled={disabled || loading}
      style={[
        styles.base,
        v.container,
        s.container,
        disabled && styles.disabled,
        style,
      ]}
      activeOpacity={0.75}
    >
      {loading ? (
        <ActivityIndicator color={v.text.color as string} />
      ) : (
        <Text style={[styles.text, v.text, s.text, textStyle]}>{title}</Text>
      )}
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  base: { borderRadius: 8, alignItems: 'center', justifyContent: 'center' },
  text: { fontWeight: '600' },
  disabled: { opacity: 0.45 },
});

export default Button;
```

**`src/components/atoms/Button/index.ts`**

```typescript
export { default } from "./Button";
export type { ButtonVariant, ButtonSize } from "./Button";
```

---

### Atom: Text

**`src/components/atoms/Text/AppText.tsx`**

```typescript
import React from 'react';
import { Text as RNText, TextStyle, StyleSheet } from 'react-native';
import { colors } from '../../../theme/colors';
import { typography } from '../../../theme/typography';

export type TextVariant =
  | 'h1'
  | 'h2'
  | 'h3'
  | 'body'
  | 'caption'
  | 'label'
  | 'error';

interface AppTextProps {
  variant?: TextVariant;
  children: React.ReactNode;
  color?: string;
  align?: 'left' | 'center' | 'right';
  style?: TextStyle;
}

const variantMap: Record<TextVariant, TextStyle> = {
  h1: {
    fontSize: typography.xxxl,
    fontWeight: '800',
    color: colors.textPrimary,
  },
  h2: {
    fontSize: typography.xxl,
    fontWeight: '700',
    color: colors.textPrimary,
  },
  h3: { fontSize: typography.xl, fontWeight: '600', color: colors.textPrimary },
  body: {
    fontSize: typography.md,
    fontWeight: '400',
    color: colors.textSecondary,
  },
  caption: {
    fontSize: typography.sm,
    fontWeight: '400',
    color: colors.textMuted,
  },
  label: {
    fontSize: typography.sm,
    fontWeight: '600',
    color: colors.textPrimary,
  },
  error: { fontSize: typography.sm, fontWeight: '400', color: colors.danger },
};

const AppText: React.FC<AppTextProps> = ({
  variant = 'body',
  children,
  color,
  align = 'left',
  style,
}) => (
  <RNText
    style={[
      variantMap[variant],
      { textAlign: align },
      color ? { color } : {},
      style,
    ]}
  >
    {children}
  </RNText>
);

export default AppText;
```

**`src/components/atoms/Text/index.ts`**

```typescript
export { default } from "./AppText";
export type { TextVariant } from "./AppText";
```

---

### Atom: Input

**`src/components/atoms/Input/Input.tsx`**

```typescript
import React, { useState } from 'react';
import {
  TextInput,
  View,
  TouchableOpacity,
  StyleSheet,
  TextInputProps,
  ViewStyle,
} from 'react-native';
import AppText from '../Text/AppText';
import { colors } from '../../../theme/colors';
import { spacing } from '../../../theme/spacing';
import { typography } from '../../../theme/typography';

interface InputProps extends TextInputProps {
  label?: string;
  error?: string;
  containerStyle?: ViewStyle;
  rightIcon?: React.ReactNode;
  onRightIconPress?: () => void;
}

const Input: React.FC<InputProps> = ({
  label,
  error,
  containerStyle,
  rightIcon,
  onRightIconPress,
  ...rest
}) => {
  const [focused, setFocused] = useState(false);

  return (
    <View style={[styles.container, containerStyle]}>
      {label && (
        <AppText variant="label" style={styles.label}>
          {label}
        </AppText>
      )}
      <View
        style={[
          styles.inputRow,
          focused && styles.focused,
          error ? styles.errorBorder : {},
        ]}
      >
        <TextInput
          style={styles.input}
          placeholderTextColor={colors.textMuted}
          onFocus={() => setFocused(true)}
          onBlur={() => setFocused(false)}
          {...rest}
        />
        {rightIcon && (
          <TouchableOpacity onPress={onRightIconPress} style={styles.icon}>
            {rightIcon}
          </TouchableOpacity>
        )}
      </View>
      {error && <AppText variant="error">{error}</AppText>}
    </View>
  );
};

const styles = StyleSheet.create({
  container: { gap: spacing.xs },
  label: { marginBottom: 2 },
  inputRow: {
    flexDirection: 'row',
    alignItems: 'center',
    borderWidth: 1,
    borderColor: colors.border,
    borderRadius: 8,
    backgroundColor: colors.inputBg,
    paddingHorizontal: spacing.sm,
  },
  focused: { borderColor: colors.primary },
  errorBorder: { borderColor: colors.danger },
  input: {
    flex: 1,
    fontSize: typography.md,
    color: colors.textPrimary,
    paddingVertical: spacing.sm,
  },
  icon: { paddingLeft: spacing.xs },
});

export default Input;
```

**`src/components/atoms/Input/index.ts`**

```typescript
export { default } from "./Input";
```

---

### Atom: Spacer

**`src/components/atoms/Spacer/Spacer.tsx`**

```typescript
import React from 'react';
import { View } from 'react-native';
import { spacing } from '../../../theme/spacing';

type SpacerSize = 'xs' | 'sm' | 'md' | 'lg' | 'xl' | 'xxl';

interface SpacerProps {
  size?: SpacerSize;
  horizontal?: boolean;
  flex?: number;
}

const Spacer: React.FC<SpacerProps> = ({
  size = 'md',
  horizontal = false,
  flex,
}) => {
  const value = spacing[size];
  if (flex !== undefined) return <View style={{ flex }} />;
  return <View style={horizontal ? { width: value } : { height: value }} />;
};

export default Spacer;
```

**`src/components/atoms/Spacer/index.ts`**

```typescript
export { default } from "./Spacer";
```

---

### Atom: Badge

**`src/components/atoms/Badge/Badge.tsx`**

```typescript
import React from 'react';
import { View, StyleSheet, ViewStyle } from 'react-native';
import AppText from '../Text/AppText';
import { colors } from '../../../theme/colors';
import { spacing } from '../../../theme/spacing';

export type BadgeVariant =
  | 'default'
  | 'success'
  | 'warning'
  | 'danger'
  | 'info';

interface BadgeProps {
  label: string;
  variant?: BadgeVariant;
  style?: ViewStyle;
}

const variantColors: Record<BadgeVariant, { bg: string; text: string }> = {
  default: { bg: colors.surface, text: colors.textPrimary },
  success: { bg: '#d1fae5', text: '#065f46' },
  warning: { bg: '#fef3c7', text: '#92400e' },
  danger: { bg: '#fee2e2', text: '#991b1b' },
  info: { bg: '#dbeafe', text: '#1e40af' },
};

const Badge: React.FC<BadgeProps> = ({ label, variant = 'default', style }) => {
  const v = variantColors[variant];
  return (
    <View style={[styles.badge, { backgroundColor: v.bg }, style]}>
      <AppText variant="caption" color={v.text}>
        {label}
      </AppText>
    </View>
  );
};

const styles = StyleSheet.create({
  badge: {
    paddingHorizontal: spacing.sm,
    paddingVertical: 2,
    borderRadius: 99,
    alignSelf: 'flex-start',
  },
});

export default Badge;
```

**`src/components/atoms/Badge/index.ts`**

```typescript
export { default } from "./Badge";
export type { BadgeVariant } from "./Badge";
```

---

### Atom: Divider

**`src/components/atoms/Divider/Divider.tsx`**

```typescript
import React from 'react';
import { View, StyleSheet, ViewStyle } from 'react-native';
import { colors } from '../../../theme/colors';

interface DividerProps {
  color?: string;
  thickness?: number;
  style?: ViewStyle;
}

const Divider: React.FC<DividerProps> = ({
  color = colors.border,
  thickness = 1,
  style,
}) => (
  <View
    style={[styles.line, { backgroundColor: color, height: thickness }, style]}
  />
);

const styles = StyleSheet.create({
  line: { width: '100%' },
});

export default Divider;
```

**`src/components/atoms/Divider/index.ts`**

```typescript
export { default } from "./Divider";
```

---

## Phase 6 — Generate Molecules

### Molecule: Card

**`src/components/molecules/Card/Card.tsx`**

```typescript
import React from 'react';
import { View, StyleSheet, ViewStyle } from 'react-native';
import { colors } from '../../../theme/colors';
import { spacing } from '../../../theme/spacing';

interface CardProps {
  children: React.ReactNode;
  style?: ViewStyle;
  elevated?: boolean;
}

const Card: React.FC<CardProps> = ({ children, style, elevated = true }) => (
  <View style={[styles.card, elevated && styles.shadow, style]}>
    {children}
  </View>
);

const styles = StyleSheet.create({
  card: {
    backgroundColor: colors.surface,
    borderRadius: 12,
    padding: spacing.md,
  },
  shadow: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.08,
    shadowRadius: 8,
    elevation: 3,
  },
});

export default Card;
```

**`src/components/molecules/Card/index.ts`**

```typescript
export { default } from "./Card";
```

---

### Molecule: FormField

**`src/components/molecules/FormField/FormField.tsx`**

```typescript
import React from 'react';
import { View, StyleSheet, ViewStyle } from 'react-native';
import Input from '../../atoms/Input/Input';
import AppText from '../../atoms/Text/AppText';
import { spacing } from '../../../theme/spacing';
import { TextInputProps } from 'react-native';

interface FormFieldProps extends TextInputProps {
  label: string;
  error?: string;
  hint?: string;
  containerStyle?: ViewStyle;
}

const FormField: React.FC<FormFieldProps> = ({
  label,
  error,
  hint,
  containerStyle,
  ...rest
}) => (
  <View style={[styles.container, containerStyle]}>
    <Input label={label} error={error} {...rest} />
    {hint && !error && (
      <AppText variant="caption" style={styles.hint}>
        {hint}
      </AppText>
    )}
  </View>
);

const styles = StyleSheet.create({
  container: { marginBottom: spacing.md },
  hint: { marginTop: 4 },
});

export default FormField;
```

**`src/components/molecules/FormField/index.ts`**

```typescript
export { default } from "./FormField";
```

---

### Molecule: ListItem

**`src/components/molecules/ListItem/ListItem.tsx`**

```typescript
import React from 'react';
import { TouchableOpacity, View, StyleSheet } from 'react-native';
import AppText from '../../atoms/Text/AppText';
import Divider from '../../atoms/Divider/Divider';
import { spacing } from '../../../theme/spacing';

interface ListItemProps {
  title: string;
  subtitle?: string;
  left?: React.ReactNode;
  right?: React.ReactNode;
  onPress?: () => void;
  showDivider?: boolean;
}

const ListItem: React.FC<ListItemProps> = ({
  title,
  subtitle,
  left,
  right,
  onPress,
  showDivider = true,
}) => (
  <>
    <TouchableOpacity onPress={onPress} style={styles.row} activeOpacity={0.7}>
      {left && <View style={styles.left}>{left}</View>}
      <View style={styles.content}>
        <AppText variant="label">{title}</AppText>
        {subtitle && <AppText variant="caption">{subtitle}</AppText>}
      </View>
      {right && <View style={styles.right}>{right}</View>}
    </TouchableOpacity>
    {showDivider && <Divider />}
  </>
);

const styles = StyleSheet.create({
  row: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingVertical: spacing.sm,
  },
  left: { marginRight: spacing.sm },
  content: { flex: 1 },
  right: { marginLeft: spacing.sm },
});

export default ListItem;
```

**`src/components/molecules/ListItem/index.ts`**

```typescript
export { default } from "./ListItem";
```

---

### Molecule: SearchBar

**`src/components/molecules/SearchBar/SearchBar.tsx`**

```typescript
import React from 'react';
import { View, TextInput, StyleSheet, ViewStyle } from 'react-native';
import { colors } from '../../../theme/colors';
import { spacing } from '../../../theme/spacing';
import { typography } from '../../../theme/typography';

interface SearchBarProps {
  value: string;
  onChangeText: (text: string) => void;
  placeholder?: string;
  style?: ViewStyle;
}

const SearchBar: React.FC<SearchBarProps> = ({
  value,
  onChangeText,
  placeholder = 'Search...',
  style,
}) => (
  <View style={[styles.container, style]}>
    <TextInput
      value={value}
      onChangeText={onChangeText}
      placeholder={placeholder}
      placeholderTextColor={colors.textMuted}
      style={styles.input}
    />
  </View>
);

const styles = StyleSheet.create({
  container: {
    backgroundColor: colors.inputBg,
    borderRadius: 10,
    paddingHorizontal: spacing.md,
    paddingVertical: spacing.sm,
    borderWidth: 1,
    borderColor: colors.border,
  },
  input: { fontSize: typography.md, color: colors.textPrimary },
});

export default SearchBar;
```

**`src/components/molecules/SearchBar/index.ts`**

```typescript
export { default } from "./SearchBar";
```

---

## Phase 7 — Theme Files

**`src/theme/colors.ts`**

```typescript
export const colors = {
  primary: "#6366f1",
  secondary: "#8b5cf6",
  danger: "#ef4444",
  success: "#22c55e",
  warning: "#f59e0b",
  info: "#3b82f6",
  white: "#ffffff",
  black: "#000000",
  surface: "#ffffff",
  background: "#f9fafb",
  inputBg: "#f3f4f6",
  border: "#e5e7eb",
  textPrimary: "#111827",
  textSecondary: "#374151",
  textMuted: "#9ca3af",
};
```

**`src/theme/spacing.ts`**

```typescript
export const spacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  xxl: 48,
};
```

**`src/theme/typography.ts`**

```typescript
export const typography = {
  xs: 10,
  sm: 12,
  md: 14,
  lg: 16,
  xl: 18,
  xxl: 22,
  xxxl: 28,
};
```

**`src/theme/index.ts`**

```typescript
export { colors } from "./colors";
export { spacing } from "./spacing";
export { typography } from "./typography";
```

---

## Phase 8 — Component Example Screen

Create `src/screens/ComponentShowcase/ComponentShowcaseScreen.tsx` — a scrollable screen demonstrating every atom and molecule with real usage:

```typescript
import React, { useState } from 'react';
import { ScrollView, StyleSheet, View } from 'react-native';
import { SafeAreaView } from 'react-native-safe-area-context';

import Button from '../../components/atoms/Button/Button';
import AppText from '../../components/atoms/Text/AppText';
import Input from '../../components/atoms/Input/Input';
import Spacer from '../../components/atoms/Spacer/Spacer';
import Badge from '../../components/atoms/Badge/Badge';
import Divider from '../../components/atoms/Divider/Divider';
import Card from '../../components/molecules/Card/Card';
import FormField from '../../components/molecules/FormField/FormField';
import ListItem from '../../components/molecules/ListItem/ListItem';
import SearchBar from '../../components/molecules/SearchBar/SearchBar';

import { colors } from '../../theme/colors';
import { spacing } from '../../theme/spacing';

const Section: React.FC<{ title: string; children: React.ReactNode }> = ({
  title,
  children,
}) => (
  <View style={styles.section}>
    <AppText variant="h3">{title}</AppText>
    <Spacer size="sm" />
    {children}
    <Spacer size="lg" />
    <Divider />
  </View>
);

const ComponentShowcaseScreen: React.FC = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [search, setSearch] = useState('');
  const [emailError, setEmailError] = useState('');

  const validateEmail = (text: string) => {
    setEmail(text);
    setEmailError(
      text && !text.includes('@') ? 'Enter a valid email address' : '',
    );
  };

  return (
    <SafeAreaView style={styles.safe}>
      <ScrollView contentContainerStyle={styles.scroll}>
        <AppText variant="h1">Component Showcase</AppText>
        <AppText variant="body">
          Every atom and molecule with live examples.
        </AppText>
        <Spacer size="lg" />

        {/* ── Buttons ───────────────────────────────────── */}
        <Section title="Button — Atom">
          <Button title="Primary" onPress={() => {}} />
          <Spacer size="sm" />
          <Button title="Secondary" variant="secondary" onPress={() => {}} />
          <Spacer size="sm" />
          <Button title="Outline" variant="outline" onPress={() => {}} />
          <Spacer size="sm" />
          <Button title="Ghost" variant="ghost" onPress={() => {}} />
          <Spacer size="sm" />
          <Button title="Danger" variant="danger" onPress={() => {}} />
          <Spacer size="sm" />
          <Button title="Loading…" loading onPress={() => {}} />
          <Spacer size="sm" />
          <Button title="Disabled" disabled onPress={() => {}} />
          <Spacer size="sm" />
          <View style={styles.row}>
            <Button
              title="SM"
              size="sm"
              onPress={() => {}}
              style={styles.flex}
            />
            <Spacer horizontal size="sm" />
            <Button
              title="MD"
              size="md"
              onPress={() => {}}
              style={styles.flex}
            />
            <Spacer horizontal size="sm" />
            <Button
              title="LG"
              size="lg"
              onPress={() => {}}
              style={styles.flex}
            />
          </View>
        </Section>

        {/* ── Text ──────────────────────────────────────── */}
        <Section title="Text — Atom">
          <AppText variant="h1">Heading 1</AppText>
          <AppText variant="h2">Heading 2</AppText>
          <AppText variant="h3">Heading 3</AppText>
          <AppText variant="body">
            Body — regular paragraph text used throughout the app.
          </AppText>
          <AppText variant="label">Label</AppText>
          <AppText variant="caption">Caption — secondary information</AppText>
          <AppText variant="error">Error — something went wrong</AppText>
        </Section>

        {/* ── Input ─────────────────────────────────────── */}
        <Section title="Input — Atom">
          <Input placeholder="Default input" value="" onChangeText={() => {}} />
          <Spacer size="sm" />
          <Input
            label="With label"
            placeholder="Enter value"
            value=""
            onChangeText={() => {}}
          />
          <Spacer size="sm" />
          <Input
            label="With error"
            placeholder="Enter value"
            value="bad input"
            onChangeText={() => {}}
            error="This field is required"
          />
        </Section>

        {/* ── Badge ─────────────────────────────────────── */}
        <Section title="Badge — Atom">
          <View style={styles.row}>
            <Badge label="Default" />
            <Spacer horizontal size="sm" />
            <Badge label="Success" variant="success" />
            <Spacer horizontal size="sm" />
            <Badge label="Warning" variant="warning" />
            <Spacer horizontal size="sm" />
            <Badge label="Danger" variant="danger" />
            <Spacer horizontal size="sm" />
            <Badge label="Info" variant="info" />
          </View>
        </Section>

        {/* ── Divider ───────────────────────────────────── */}
        <Section title="Divider — Atom">
          <Divider />
          <Spacer size="sm" />
          <Divider color={colors.primary} thickness={2} />
          <Spacer size="sm" />
          <Divider color={colors.danger} thickness={1} />
        </Section>

        {/* ── Card ──────────────────────────────────────── */}
        <Section title="Card — Molecule">
          <Card>
            <AppText variant="h3">Card Title</AppText>
            <Spacer size="xs" />
            <AppText variant="body">
              Cards wrap any content in an elevated surface. Combine atoms
              inside freely.
            </AppText>
            <Spacer size="sm" />
            <Badge label="New" variant="info" />
          </Card>
          <Spacer size="sm" />
          <Card
            elevated={false}
            style={{ borderWidth: 1, borderColor: colors.border }}
          >
            <AppText variant="label">Flat Card (no shadow)</AppText>
          </Card>
        </Section>

        {/* ── SearchBar ─────────────────────────────────── */}
        <Section title="SearchBar — Molecule">
          <SearchBar value={search} onChangeText={setSearch} />
        </Section>

        {/* ── FormField ─────────────────────────────────── */}
        <Section title="FormField — Molecule">
          <FormField
            label="Email"
            placeholder="you@example.com"
            value={email}
            onChangeText={validateEmail}
            error={emailError}
            hint="We'll never share your email."
            keyboardType="email-address"
            autoCapitalize="none"
          />
          <FormField
            label="Password"
            placeholder="••••••••"
            value={password}
            onChangeText={setPassword}
            secureTextEntry
          />
          <Button title="Submit" onPress={() => {}} />
        </Section>

        {/* ── ListItem ──────────────────────────────────── */}
        <Section title="ListItem — Molecule">
          <ListItem
            title="Profile"
            subtitle="Manage your account"
            onPress={() => {}}
          />
          <ListItem
            title="Notifications"
            subtitle="Push, email & SMS"
            right={<Badge label="3" variant="danger" />}
            onPress={() => {}}
          />
          <ListItem title="Sign Out" onPress={() => {}} showDivider={false} />
        </Section>
      </ScrollView>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  safe: { flex: 1, backgroundColor: colors.background },
  scroll: { padding: spacing.md },
  section: { marginTop: spacing.md },
  row: { flexDirection: 'row', flexWrap: 'wrap', alignItems: 'center' },
  flex: { flex: 1 },
});

export default ComponentShowcaseScreen;
```

---

## Phase 9 — Root Barrel Files

**`src/components/atoms/index.ts`**

```typescript
export { default as Button } from "./Button";
export { default as AppText } from "./Text";
export { default as Input } from "./Input";
export { default as Spacer } from "./Spacer";
export { default as Badge } from "./Badge";
export { default as Divider } from "./Divider";
```

**`src/components/molecules/index.ts`**

```typescript
export { default as Card } from "./Card";
export { default as FormField } from "./FormField";
export { default as ListItem } from "./ListItem";
export { default as SearchBar } from "./SearchBar";
```

**`src/components/index.ts`**

```typescript
export * from "./atoms";
export * from "./molecules";
```
