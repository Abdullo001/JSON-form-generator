# Vue 3 + Vuetify Dynamic Form Generator

A powerful Vue 3 application that generates forms dynamically based on JSON configuration files. Built with Vue 3 Composition API and Vuetify 3.

## Features

- **Dynamic Form Generation**: Forms are completely defined by JSON configuration
- **Multiple Field Types**: Support for text, email, number, select, radio, checkbox, date, and textarea fields
- **Cascading Selects**: Dependent dropdowns that update based on parent field selections
- **Responsive Layout**: Configurable column layouts for different screen sizes
- **Form Validation**: Built-in validation rules with customizable error messages
- **Modern UI**: Beautiful interface built with Vuetify 3 components

## Supported Field Types

1. **Text Field** (`text`) - Single line text input
2. **Email Field** (`email`) - Email input with validation
3. **Number Field** (`number`) - Numeric input with min/max validation
4. **Select Field** (`select`) - Dropdown selection with options
5. **Radio Group** (`radio`) - Radio button group selection
6. **Checkbox** (`checkbox`) - Single checkbox or multiple checkboxes
7. **Date Picker** (`date`) - Date input field
8. **Textarea** (`textarea`) - Multi-line text input

## Installation

1. Install dependencies:
```bash
npm install
```

2. Start the development server:
```bash
npm run dev
```

3. Open your browser and navigate to `http://localhost:3000`

## JSON Configuration Structure

The form configuration follows this structure:

```json
{
  "title": "Form Title",
  "description": "Form Description",
  "layout": [
    {
      "fields": [
        {
          "type": "text",
          "name": "fieldName",
          "label": "Field Label",
          "placeholder": "Placeholder text",
          "required": true,
          "cols": 6,
          "sm": 6,
          "md": 6,
          "lg": 6,
          "minLength": 2,
          "maxLength": 50,
          "defaultValue": "default value"
        }
      ]
    }
  ],
  "submitButton": {
    "text": "Submit",
    "color": "primary"
  }
}
```

### Field Properties

- `type`: Field type (text, email, number, select, radio, checkbox, date, textarea)
- `name`: Unique field identifier
- `label`: Display label for the field
- `placeholder`: Placeholder text
- `required`: Whether the field is required
- `cols`, `sm`, `md`, `lg`: Responsive column sizes (1-12)
- `minLength`, `maxLength`: Text length validation
- `min`, `max`: Number range validation
- `options`: Array of options for select/radio fields
- `dependsOn`: Configuration for dependent fields
- `dependencyOptions`: Options based on parent field value

### Cascading Selects

To create dependent dropdowns, use the `dependsOn` and `dependencyOptions` properties:

```json
{
  "type": "select",
  "name": "state",
  "label": "State",
  "dependsOn": {
    "field": "country"
  },
  "dependencyOptions": {
    "us": [
      { "value": "ny", "label": "New York" },
      { "value": "ca", "label": "California" }
    ],
    "uk": [
      { "value": "england", "label": "England" },
      { "value": "scotland", "label": "Scotland" }
    ]
  }
}
```

## Usage

### Basic Usage

```vue
<template>
  <FormRenderer 
    :config="formConfig" 
    @submit="handleFormSubmit"
  />
</template>

<script setup>
import FormRenderer from './components/FormRenderer.vue'
import formConfig from './config/formConfig.json'

const handleFormSubmit = (formData) => {
  console.log('Form data:', formData)
  // Handle form submission
}
</script>
```

### Props

- `config` (Object, required): The JSON configuration object

### Events

- `submit`: Emitted when the form is submitted with the form data

## Example Configuration

The project includes a comprehensive example configuration in `src/config/formConfig.json` that demonstrates:

- Multiple field types
- Responsive layout with different column sizes
- Cascading selects (Country → State → City)
- Form validation rules
- Required and optional fields
- Default values

## Project Structure

```
src/
├── components/
│   └── FormRenderer.vue    # Main form rendering component
├── config/
│   └── formConfig.json     # Example form configuration
├── App.vue                 # Main application component
└── main.js                 # Application entry point
```

## Technologies Used

- **Vue 3**: Progressive JavaScript framework with Composition API
- **Vuetify 3**: Material Design component framework
- **Vite**: Fast build tool and development server
- **Material Design Icons**: Icon library

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)

## License

MIT License
# JSON-form-generator
