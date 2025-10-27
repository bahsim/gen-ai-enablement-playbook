# Legacy HOC Package - Utility Package

## Package Overview

**Purpose**: Provides legacy Higher-Order Component (HOC) utilities for the BigCommerce checkout system, supporting legacy component patterns and injection mechanisms.

**Architecture**: Utility package containing HOC creation utilities, injection mechanisms, and mappable HOC patterns for legacy component support.

## Key Responsibilities

### 1. HOC Creation
- **createInjectHoc**: Create injection HOC for component enhancement
- **createMappableInjectHoc**: Create mappable injection HOC
- **HOC Factory**: HOC factory pattern implementation
- **Component Enhancement**: Component enhancement through HOCs

### 2. Injection Mechanisms
- **InjectHoc**: Base injection HOC implementation
- **MatchedProps**: Matched props type definitions
- **Injection Pattern**: Component injection pattern
- **Props Enhancement**: Props enhancement through injection

### 3. Mappable HOCs
- **MappableInjectHoc**: Mappable injection HOC implementation
- **MapToProps**: Props mapping function type
- **MapToPropsFactory**: Props mapping factory type
- **Dynamic Mapping**: Dynamic props mapping

## Component Structure

### Main Components
- **InjectHoc**: Base injection HOC component
- **createInjectHoc**: HOC creation utility
- **createMappableInjectHoc**: Mappable HOC creation utility
- **MappableInjectHoc**: Mappable injection HOC component

### Type Definitions
- **MatchedProps**: Matched props type definitions
- **MapToProps**: Props mapping function type
- **MapToPropsFactory**: Props mapping factory type

### Testing Components
- **createInjectHoc.test.tsx**: HOC creation testing
- **HOC Tests**: HOC functionality testing

## Integration Points

### Core Package Integration
- **Legacy Components**: Used by legacy React components
- **Component Enhancement**: Component enhancement through HOCs
- **Props Injection**: Props injection and enhancement
- **Legacy Patterns**: Legacy component pattern support

### External Usage
- **Component Libraries**: Used by component libraries
- **Legacy Systems**: Legacy system integration
- **Component Enhancement**: External component enhancement

## HOC Patterns

### Injection HOC Pattern
- **Component Wrapping**: Component wrapping with HOC
- **Props Injection**: Props injection into wrapped components
- **Enhancement**: Component functionality enhancement
- **Legacy Support**: Legacy component support

### Mappable HOC Pattern
- **Props Mapping**: Dynamic props mapping
- **Factory Pattern**: HOC factory pattern
- **Dynamic Enhancement**: Dynamic component enhancement
- **Flexible Injection**: Flexible props injection

## HOC Creation Flow

### 1. HOC Definition
- Define HOC requirements and functionality
- Create HOC factory function
- Configure injection mechanisms

### 2. Component Wrapping
- Wrap target component with HOC
- Inject required props and functionality
- Enhance component capabilities

### 3. Props Mapping
- Map props from parent to child components
- Transform props as needed
- Provide enhanced props to wrapped component

### 4. Component Enhancement
- Add additional functionality to wrapped component
- Provide legacy pattern support
- Maintain component compatibility

## Testing Strategy

### Unit Testing
- **HOC Creation Tests**: HOC creation utility testing
- **Injection Tests**: Props injection testing
- **Mapping Tests**: Props mapping testing
- **Enhancement Tests**: Component enhancement testing

### Integration Testing
- **Component Integration Tests**: HOC component integration testing
- **Legacy System Tests**: Legacy system integration testing
- **Props Flow Tests**: Props flow testing

### Test Coverage
- **Comprehensive Coverage**: Full test coverage for all HOCs
- **Edge Cases**: Edge case testing
- **Legacy Compatibility**: Legacy compatibility testing

## Configuration

### HOC Configuration
- **Injection Options**: Props injection configuration
- **Mapping Options**: Props mapping configuration
- **Enhancement Options**: Component enhancement configuration

### Legacy Support Configuration
- **Legacy Patterns**: Legacy pattern support configuration
- **Compatibility Options**: Compatibility configuration
- **Migration Options**: Migration support configuration

## Performance Considerations

### HOC Performance
- **Component Wrapping**: Efficient component wrapping
- **Props Injection**: Efficient props injection
- **Memory Management**: Proper memory management
- **Re-render Optimization**: Re-render optimization

### Legacy Performance
- **Legacy Optimization**: Legacy component optimization
- **Compatibility Performance**: Compatibility performance
- **Migration Performance**: Migration performance

## Browser Compatibility

### Supported Browsers
- **Chrome**: Full HOC support
- **Safari**: Full HOC support
- **Firefox**: Full HOC support
- **Edge**: Full HOC support

### Feature Detection
- **HOC Support**: HOC support detection
- **Legacy Support**: Legacy component support detection
- **Injection Support**: Props injection support detection

## Maintenance Notes

### Common Issues
- **HOC Performance**: HOC performance issues
- **Props Injection**: Props injection edge cases
- **Legacy Compatibility**: Legacy compatibility issues
- **Memory Leaks**: Memory leak prevention

### Future Considerations
- **Modern Patterns**: Migration to modern React patterns
- **HOC Optimization**: HOC performance optimization
- **Legacy Migration**: Legacy component migration
- **Enhanced HOCs**: Enhanced HOC capabilities
