# @solisoma/svelte-form

A flexible form package for Svelte applications. It handles form validation, state management, and more. Similar to Formik for React.

## Installation

```bash
npm i @solisoma/svelte-form
```

## Usage

@solisoma/svelte-form offers flexibility by allowing users to choose between two types of validation schema. Users can select the one that best suits their needs. Either the yulValidator or the schemaValidator.

```svelte
<script>
  import Form from "@solisoma/svelte-form/lib/Form.svelte";
  import ErrorMessage from "@solisoma/svelte-form/lib/ErrorMessage.svelte";
</script>

<Form {initialValue} {yupValidator}>
  <input
    type="text"
    placeholder="Name"
    name="name"
    class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
  />
  <ErrorMessage For="name" />
</Form>
```

or

```svelte
<script>
  import Form from "@solisoma/svelte-form/lib/Form.svelte";
  import ErrorMessage from "@solisoma/svelte-form/lib/ErrorMessage.svelte";
</script>

<Form {initialValue} {schemaValidator}>
  <input
    type="text"
    placeholder="Name"
    name="name"
    class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
  />
  <ErrorMessage For="name" />
</Form>
```

## Props

- `initialValue`: Initial values for the form fields.
- `schemaValidator`: Custom schema validator function.
- `yupValidator`: Yup validation schema.

## Events

- `submit`: Triggered when the form is submitted. The event handler receives the form values as an argument.

## Features

- Form validation.
- State management.
- Flexibility for customization.

## Example

### Using `yupValidator` for the validation schema

```svelte
<script>
  import * as Yup from "yup";
  import Form from "@solisoma/svelte-form/lib/Form.svelte";
  import ErrorMessage from "@solisoma/svelte-form/lib/ErrorMessage.svelte";

  function handleSubmit({ detail }) {
    //Logic
  }

  let initialValue = {
    name: "",
    amount: "",
    date: "",
    desc: "",
    type: "inflow",
    submit: false,
  };


  const yupValidator = Yup.object({
    name: Yup.string()
      .required("This field is required")
      .max(25, "Must be 25 characters or less"),
    amount: Yup.string()
      .required()
      .required("This field is required")
      .matches(/^[0-9]+$/, "Must be a positive number"),
    date: Yup.string().required("This field is required"),
    desc: Yup.string().required("This field is required"),
  });
</script>

<div>
  <h2 class="font-bold text-2xl mb-3">Add new statement</h2>
  <div
    class="flex flex-col justify-between overflow-y-scroll remove-scrollbar h-[67vh] pb-2"
  >
    <Form
      {yupValidator}
      {initialValue}
      on:submit={handleSubmit}
    >
      <div class="flex flex-col gap-3">
        <input
          type="text"
          placeholder="Name"
          name="name"
          class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
        />
        <ErrorMessage For="name" />
        <input
          type="text"
          placeholder="Amount"
          name="amount"
          class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
        />
        <ErrorMessage For="amount" />
        <textarea
          placeholder="Description"
          name="desc"
          class="p-3 h-[20vh] bg-white entry-field border-2 border-gray-300 rounded-lg outline-none resize-none"
        />
        <ErrorMessage For="desc" />
        <div class="flex gap-3 items-center">
          <select
            name="type"
            class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
          >
            <option value="inflow">In-flow</option>
            <option value="outflow">Out-flow</option>
          </select>
          <input
            type="date"
            placeholder="Date"
            name="date"
            class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
          />
          <ErrorMessage For="date" />
        </div>
      </div>
      <div class="flex justify-center mb-6">
        <button
          type="submit"
          class="text-white !bg-blue-700 p-2 rounded-lg w-[40vw] font-semibold mt-2"
        >
          ADD
        </button>
      </div>
    </Form>
  </div>
</div>
```

The extension includes an `ErrorMessage` component designed to handle error messages for form inputs. It is recommended that each input field in your form is accompanied by an `ErrorMessage` component positioned below it. This setup allows for the display of relevant error messages if validation fails.

The `ErrorMessage` component accepts a prop called `For`. This prop serves as a link between each `ErrorMessage` instance and its associated input element. The value of the `For` prop should match the `name` attribute of the corresponding input field, ensuring that error messages are correctly linked to their respective inputs.

### Using `schemaValidator` for the validation schema

The `schemaValidator` offers an alternative approach to defining the validation schema for forms. It adheres to a LinkedList-like structure, comprising key-value pairs along with a `next` property. Each key-value pair represents a validation rule associated with a specific input field, while the `next` property indicates the subsequent validation rule in the sequence. This format facilitates clear and sequential validation rule declaration for each input field in the form.

```svelte
<script>
  // import * as Yup from "yup";
  import Form from "@solisoma/svelte-form/lib/Form.svelte";
  import ErrorMessage from "@solisoma/svelte-form/lib/ErrorMessage.svelte";

  function handleSubmit({ detail }) {
    //Logic
  }

  let initialValue = {
    name: "",
    amount: "",
    date: "",
    desc: "",
    type: "inflow",
    submit: false,
  };

  const schemaValidator = {
    name: {
      key: "string",
      value: null,
      next: {
        key: "required",
        value: "This field is required",
        next: { key: "max", value: [25, "Must be 25 characters or less"] },
      },
    },
    amount: {
      key: "string",
      value: null,
      next: {
        key: "required",
        value: "This field is required",
        next: {
          key: "matches",
          value: [/^[0-9]+$/, "Must be a positive number"],
        },
      },
    },
    date: {
      key: "string",
      value: null,
      next: {
        key: "required",
        value: "This field is required",
      },
    },
    desc: {
      key: "string",
      value: null,
      next: {
        key: "required",
        value: "This field is required",
      },
    },
  };
</script>

<div>
  <h2 class="font-bold text-2xl mb-3">Add new statement</h2>
  <div
    class="flex flex-col justify-between overflow-y-scroll remove-scrollbar h-[67vh] pb-2"
  >
    <Form
      {schemaValidator}
      {initialValue}
      on:submit={handleSubmit}
    >
      <div class="flex flex-col gap-3">
        <input
          type="text"
          placeholder="Name"
          name="name"
          class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
        />
        <ErrorMessage For="name" />
        <input
          type="text"
          placeholder="Amount"
          name="amount"
          class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
        />
        <ErrorMessage For="amount" />
        <textarea
          placeholder="Description"
          name="desc"
          class="p-3 h-[20vh] bg-white entry-field border-2 border-gray-300 rounded-lg outline-none resize-none"
        />
        <ErrorMessage For="desc" />
        <div class="flex gap-3 items-center">
          <select
            name="type"
            class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
          >
            <option value="inflow">In-flow</option>
            <option value="outflow">Out-flow</option>
          </select>
          <input
            type="date"
            placeholder="Date"
            name="date"
            class="p-3 bg-white entry-field border-2 border-gray-300 rounded-lg outline-none"
          />
          <ErrorMessage For="date" />
        </div>
      </div>
      <div class="flex justify-center mb-6">
        <button
          type="submit"
          class="text-white !bg-blue-700 p-2 rounded-lg w-[40vw] font-semibold mt-2"
        >
          ADD
        </button>
      </div>
    </Form>
  </div>
</div>
```

## Rules in schemaValidator

The `schemaValidator` offers a comprehensive set of commonly used validation rules, including `string`, `required`, `min`, `max`, `email`, `matches`, `oneOf`, `integer`, `positive`, `negative`, `url`, and `date`. These predefined rules cover a wide range of typical validation scenarios, ensuring ease of use and versatility. However, if your validation requirements extend beyond these predefined rules, you can seamlessly integrate the `yupValidator` for more customized validation logic.

Let's break down each rule and clarify its expected value:

- **`string`:** This rule expects `null` as its value, indicating that the input should be of string type. Example usage:

```javascript
const schemaValidator = {
  name: { key: "string", value: null, next: null },
};
```

- **`required`:** For mandatory fields, use the `required` rule. It expects a string as its value, which represents the error message to display if the field is left empty. Example usage:

```javascript
const schemaValidator = {
  name: { key: "required", value: "This field is required", next: null },
};
```

- **`min` and `max`:** These rules expect an array as their value. The array contains two elements: the minimum or maximum length allowed for the input, and the corresponding error message. Example usage for `min`:

```javascript
const schemaValidator = {
  name: {
    key: "min",
    value: [12, "Characters should not be less than 12"],
    next: null,
  },
};
```

For `max`, it's similar, but it defines the maximum length instead.

- **`email`:** For validating email formats, use the `email` rule. It expects a string as its value, representing the error message for invalid email formats. Example usage:

```javascript
const schemaValidator = {
  name: {
    key: "email",
    value: "Invalid email format",
    next: null,
  },
};
```

- **`matches`:** This rule validates input based on a regular expression pattern. It expects an array as its value, where the first element is the regex pattern, and the second is the error message. Example usage:

```javascript
const schemaValidator = {
  name: {
    key: "matches",
    value: [/^[0-9]+$/, "Must be a positive number"],
    next: null,
  },
};
```

- **`oneOf`:** For fields with a predefined set of allowed values, use the `oneOf` rule. It expects an array containing the allowed values. Example usage:

```javascript
const schemaValidator = {
  name: {
    key: "oneOf",
    value: ["active", "inactive", "pending"],
    next: null,
  },
};
```

- **`integer`:** This rule expects a string as its value, representing the error message to display if the input is not a valid integer. Example usage:

```javascript
const schemaValidator = {
  name: {
    key: "integer",
    value: "Must be a number",
    next: null,
  },
};
```

- **`positive`:** For ensuring that the input is a positive number, use the `positive` rule. It expects a string as its value, indicating the error message for negative numbers. Example usage:

```javascript
const schemaValidator = {
  name: {
    key: "positive",
    value: "Must be a positive number",
    next: null,
  },
};
```

- **`negative`:** Similarly, to validate that the input is a negative number, utilize the `negative` rule. It expects a string as its value, representing the error message for positive numbers. Example usage:

```javascript
const schemaValidator = {
  name: {
    key: "negative",
    value: "Must be a negative number",
    next: null,
  },
};
```

- **`url`:** This rule ensures that the input follows a valid URL format. It expects a string as its value, representing the error message to display if the URL format is incorrect. Example usage:

```javascript
const schemaValidator = {
  website: {
    key: "url",
    value: "Invalid URL format",
    next: null,
  },
};
```

- **`date`:** Use the `date` rule to validate that the input is a valid date. It expects null as its value, as it doesn't require additional parameters. Example usage:

```javascript
const schemaValidator = {
  dob: {
    key: "date",
    value: null,
    next: null,
  },
};
```
