<script>
  import * as Yup from "yup";
  import "../app.css";
  import { createEventDispatcher } from "svelte";
  export let initialValue = {};
  export let schemaValidator = {};
  export let yupValidator = null;

  let values = { ...initialValue };
  const dispatch = createEventDispatcher();

  $: if (!yupValidator) {
    // create a yup object
    const yupObject = { ...schemaValidator };
    Object.keys(schemaValidator).map((validator) => {
      yupObject[validator] = setYup(schemaValidator[validator], Yup);
    });

    yupValidator = Yup.object(yupObject);
  }

  // functions
  function setYup(validator, schema) {
    switch (validator.key) {
      case "string":
        schema = schema.string();
        break;
      case "required":
        schema = schema.required(validator.value);
        break;
      case "min":
        schema = schema.min(...validator.value);
        break;
      case "max":
        schema = schema.max(...validator.value);
        break;
      case "email":
        schema = schema.email(validator.value);
        break;
      case "matches":
        schema = schema.matches(...validator.value);
        break;
      case "oneOf":
        schema = schema.oneOf(...validator.value);
        break;
      case "integer":
        schema = schema.integer(validator.value);
        break;
      case "positive":
        schema = schema.positive(validator.value);
        break;
      case "negative":
        schema = schema.negative(validator.value);
        break;
      case "url":
        schema = schema.url(validator.value);
        break;
      case "date":
        schema = schema.date(...validator.value);
        break;
      default:
        throw new Error(
          `The key '${validator.key}' does not exist in the schemaValidator. Keys should be one of the following types: string, required, min, max, email, matches, oneOf, integer, positive, negative, url, or date.`
        );
    }

    if (validator.next) {
      return setYup(validator.next, schema);
    }
    return schema;
  }

  async function validateForm(err) {
    err.preventDefault();
    try {
      // Make all error mesage invisible
      const errors = document.querySelectorAll(".solisoma-form-error");
      const fields = document.querySelectorAll(".solisoma-form-field");

      errors.forEach((error) => (error.style.display = "none"));
      fields.forEach((field) => field.classList.remove("solisoma-form-field"));

      // Validate user input
      await yupValidator.validate(values);
      dispatch("submit", values);
      values = { ...initialValue };
    } catch (e) {
      const elem = document.getElementById(`${e.path}@solisoma@error`);
      const field = document.querySelector(`[name="${e.path}"]`);
      field.classList.add("solisoma-form-field");
      elem.innerText = e.errors[0];
      elem.style.display = "block";
    }
  }

  function updateForm(e) {
    const { name } = e.target;
    const value = e.target.value
      ? e.target.value
      : e.target.files
        ? e.target.files
        : e.target.checked || null;
    values = { ...values, [name]: value };
  }
</script>

<form on:submit={validateForm} on:change={updateForm}>
  <slot />
</form>
