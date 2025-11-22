# Configuración de Supabase para DERMA-IA

## 🚀 Pasos para configurar Supabase

### 1. Crear cuenta en Supabase
1. Ve a [supabase.com](https://supabase.com)
2. Crea una cuenta gratuita
3. Crea un nuevo proyecto

### 2. Configurar la base de datos
Ejecuta estos comandos SQL en el editor SQL de Supabase:

```sql
-- Crear tabla para el historial
CREATE TABLE history_items (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  type TEXT NOT NULL CHECK (type IN ('analysis', 'consultation')),
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  image_url TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Crear índices para mejor rendimiento
CREATE INDEX idx_history_items_user_id ON history_items(user_id);
CREATE INDEX idx_history_items_created_at ON history_items(created_at DESC);

-- Habilitar Row Level Security (RLS)
ALTER TABLE history_items ENABLE ROW LEVEL SECURITY;

-- Crear políticas de seguridad
CREATE POLICY "Users can view their own history" ON history_items
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can insert their own history" ON history_items
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update their own history" ON history_items
  FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Users can delete their own history" ON history_items
  FOR DELETE USING (auth.uid() = user_id);
```

### 3. Configurar la aplicación
1. Ve a Settings > API en tu proyecto de Supabase
2. Copia la URL del proyecto y la clave anónima
3. Actualiza el archivo `lib/supabase.ts`:

```typescript
const supabaseUrl = 'https://tu-proyecto-id.supabase.co';
const supabaseAnonKey = 'tu-clave-anonima-aqui';
```

### 4. Configurar autenticación (opcional)
En Supabase Dashboard > Authentication > Settings:
- Habilita "Enable email confirmations" si quieres verificación por email
- Configura las URLs de redirección si es necesario

## 🔧 Funcionalidades implementadas

### ✅ Autenticación
- Registro de usuarios
- Inicio de sesión
- Cierre de sesión
- Protección de rutas

### ✅ Historial
- Guardar análisis y consultas
- Obtener historial del usuario
- Eliminar elementos individuales
- Limpiar todo el historial
- Sincronización automática

### ✅ Seguridad
- Row Level Security (RLS)
- Políticas de acceso por usuario
- Autenticación JWT

## 📱 Uso de la aplicación

1. **Primera vez**: El usuario ve la pantalla de autenticación
2. **Registro**: Crear cuenta con email y contraseña
3. **Inicio de sesión**: Acceder con credenciales existentes
4. **Uso normal**: Todas las funciones están disponibles
5. **Historial**: Se guarda automáticamente en la nube
6. **Cerrar sesión**: Botón en la pantalla de historial

## 🎯 Beneficios de Supabase

- ✅ **Escalable**: Soporta millones de usuarios
- ✅ **Seguro**: RLS y autenticación integrada
- ✅ **Sincronización**: Datos disponibles en todos los dispositivos
- ✅ **Backup automático**: No se pierden datos
- ✅ **API automática**: No necesitas crear endpoints
- ✅ **Gratis**: Hasta 50,000 usuarios activos mensuales

## 🚨 Importante

**Recuerda actualizar las credenciales en `lib/supabase.ts` antes de usar la aplicación en producción.**
