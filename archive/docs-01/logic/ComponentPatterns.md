# Component Patterns

## Class Component Pattern
- Uses React class components with lifecycle methods
- Integrates with Redux via withCheckout HOC
- Manages local state and props mapping
- Implements error boundaries and loading states

```typescript
class ComponentName extends Component<Props, State> {
  async componentDidMount() {
    // Initialization logic
  }
  
  render() {
    // Render logic
  }
  
  private handleSubmit = () => {
    // Event handling
  }
}

export default withCheckout(mapToProps)(ComponentName);
```

## Functional Component Pattern
- Uses React hooks for state management
- Integrates with context providers
- Implements useCallback for performance optimization
- Uses useEffect for side effects

```typescript
const ComponentName: FunctionComponent<Props> = (props) => {
  const [state, setState] = useState();
  
  useEffect(() => {
    // Side effects
  }, []);
  
  const handleSubmit = useCallback(() => {
    // Event handling
  }, []);
  
  return (
    // JSX
  );
};

export default withContext(ComponentName);
```

## HOC Pattern
- withCheckout: Connects components to checkout service
- withAnalytics: Adds analytics tracking
- withLanguage: Provides internationalization
- withBillingAndPaymentContext: Provides billing/payment context

```typescript
const withCheckout = (mapToProps) => (Component) => {
  return (props) => {
    const checkoutProps = mapToProps(props);
    return <Component {...props} {...checkoutProps} />;
  };
};
```

## State Management Pattern
- Redux for global state management
- Local component state for UI state
- Context providers for component-specific state
- Memoized selectors for performance

```typescript
// Redux integration
const mapToComponentProps = (state) => ({
  data: getData(state),
  loading: getLoading(state)
});

// Context usage
const { state, actions } = useContext(Context);
```

## Error Handling Pattern
- Centralized error handling with onUnhandledError
- Error boundaries for component isolation
- Try-catch blocks for async operations
- Error logging and user notification

```typescript
const handleError = (error: Error) => {
  errorLogger.log(error);
  onUnhandledError(error);
};

try {
  await operation();
} catch (error) {
  handleError(error);
}
```

## Form Handling Pattern
- Formik for form state management
- Yup for validation schemas
- Custom validation functions for business rules
- Real-time validation with error display

```typescript
const FormComponent = withFormik({
  mapPropsToValues: (props) => props.initialValues,
  validationSchema: validationSchema,
  handleSubmit: (values, { setSubmitting }) => {
    // Submit logic
  }
})(Form);
```
