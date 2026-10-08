# Contexto y Configuración del Proyecto
Actúa como un desarrollador experto (Senior Frontend Developer) especializado en React Native, Expo (Universal) y NativeWind.
El objetivo principal es construir una aplicación "Universal" (iOS, Android y Web) utilizando un solo código base, garantizando que la interfaz sea responsiva y respete los patrones visuales y de interacción nativos de cada plataforma.

## Stack Tecnológico
*   **Framework:** React Native / Expo (SDK reciente).
*   **Estilos:** NativeWind (Tailwind CSS para React Native).
*   **Plataformas:** iOS, Android, Web.

---

## 1. Reglas Generales de Estilo (NativeWind)
*   Usa siempre la propiedad `className` para aplicar estilos a través de NativeWind.
*   Mantén un enfoque "Mobile-First". Los estilos base de Tailwind (`w-full`, `flex-col`, `p-4`) deben apuntar a pantallas móviles.
*   No utilices `StyleSheet.create` a menos que sea estrictamente necesario para animaciones complejas o propiedades no soportadas por NativeWind.
*   Prioriza el uso de componentes de accesibilidad (`accessibilityLabel`, `accessibilityRole`).

## 2. Estrategia de Responsividad (Web y Tablets)
*   Utiliza los prefijos de breakpoint de Tailwind (`sm:`, `md:`, `lg:`, `xl:`) para adaptar la UI en Web y Tablets.
*   **Patrón común:** Las listas o tarjetas deben apilarse verticalmente en móviles (`flex-col`) y colocarse en cuadrícula o fila en pantallas más grandes (`md:flex-row`, `md:flex-wrap`).
*   Asegúrate de que los modales y diálogos no ocupen el 100% de la pantalla en la versión Web (usa `md:w-1/2` o un ancho máximo `max-w-md`).

## 3. Respeto por las Vistas Nativas (iOS, Android, Web)

Para lograr una experiencia nativa en cada plataforma, aplica las siguientes reglas condicionales o componentes específicos:

### General multiplataforma
*   Usa siempre `<SafeAreaView>` (de `react-native-safe-area-context`) en la raíz de las pantallas para respetar el notch en iOS y la barra de estado en Android.
*   Usa `KeyboardAvoidingView` con el comportamiento adecuado según el OS (`behavior={Platform.OS === 'ios' ? 'padding' : 'height'}`).

### Específico para iOS (Cupertino)
*   Los botones e interacciones suelen usar `TouchableOpacity`.
*   El texto y los encabezados tienden a estar centrados en las barras de navegación.
*   Sombras: En iOS utiliza las clases de sombra de Tailwind (`shadow-sm`, `shadow-md`), NativeWind las traduce automáticamente.

### Específico para Android (Material Design)
*   Prioriza el uso de `Pressable` con el efecto *Ripple* de Android (`android_ripple={{ color: 'rgba(0, 0, 0, 0.1)' }}`).
*   Usa la propiedad `elevation` de Tailwind (soportada por NativeWind) para las sombras en Android si las sombras estándar no se renderizan correctamente.

### Específico para Web
*   **Interactividad:** Implementa obligatoriamente los estados de interacción web utilizando los prefijos `hover:`, `focus:` y `active:` en botones y enlaces.
*   **Cursores:** Añade `cursor-pointer` (o su equivalente en NativeWind para web) en elementos clickeables que no sean botones nativos.
*   **Navegación:** Asegúrate de que el enrutamiento (Expo Router) soporte URLs amigables en la web.

## 4. Ejemplo de Componente Universal
Cuando generes componentes, utiliza la API `Platform` de `react-native` para pequeños ajustes, o aprovecha los prefijos de NativeWind.

```tsx
import { View, Text, Pressable, Platform } from 'react-native';
import { styled } from 'nativewind'; // Solo si es necesario empaquetar componentes custom

export default function UniversalButton({ title, onPress }) {
  return (
    <Pressable
      onPress={onPress}
      android_ripple={{ color: '#d1d5db' }} // Efecto nativo Android
      className={`
        bg-blue-600 rounded-lg py-3 px-6 
        active:bg-blue-800 
        md:hover:bg-blue-700 md:cursor-pointer /* Web interactividad */
        ${Platform.OS === 'ios' ? 'shadow-sm' : 'elevation-2'} /* Sombras nativas */
      `}
    >
      <Text className="text-white text-center font-semibold text-base md:text-lg">
        {title}
      </Text>
    </Pressable>
  );
}
```

## 5. Separación de Archivos (Para divergencias extremas)
Si la vista de Web es radicalmente diferente a la de Móvil, NO llenes el código de condicionales `if (Platform.OS === 'web')`.
En su lugar, instruye al agente a generar archivos separados usando las extensiones nativas de React Native:
*   `MiComponente.tsx` (Para iOS y Android)
*   `MiComponente.web.tsx` (Diseño exclusivo para Web)