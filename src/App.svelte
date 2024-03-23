<script>
  import * as Yup from "yup";
  import Form from "../package/lib/Form.svelte";
  import ErrorMessage from "./lib/ErrorMessage.svelte";

  // function handle_change(e) {
  //   modalStates = { ...modalStates, [e.target.id]: e.target.value };
  //   modalSetStates(modalStates);
  // }

  function handleSubmit({ detail }) {
    console.log(detail);
  }

  const yupValidator = Yup.object({
    name: Yup.string()
      .required("This field is required")
      .max(25, "Must be 25 characters or less"),
    amount: Yup.string()
      .required("This field is required")
      .matches(/^[0-9]+$/, "Must be a positive number"),
    date: Yup.string().required("This field is required"),
    desc: Yup.string().required("This field is required"),
  });

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
      initialValue={{
        name: "",
        amount: "",
        date: "",
        desc: "",
        type: "inflow",
        submit: false,
      }}
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
            value="inflow"
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
        <div class="flex justify-center mb-6">
          <button
            type="submit"
            class="text-white !bg-blue-400 p-2 rounded-lg w-[40vw] font-semibold mt-2"
            >ADD
          </button>
        </div>
      </div>
    </Form>
  </div>
</div>
