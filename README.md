import React, { useState } from 'react';
import { useForm, Controller } from 'react-hook-form';

// Simple types for our basic version
interface BasicField {
  id: string;
  name: string;
  type: 'String' | 'Number';
  defaultValue: string;
}

interface FormData {
  fields: BasicField[];
}

const JSONSchemaBuilder = () => {
  const [jsonPreview, setJsonPreview] = useState({});
  
  const { control, watch, setValue, getValues } = useForm<FormData>({
    defaultValues: {
      fields: [
        {
          id: '1',
          name: 'sampleField',
          type: 'String',
          defaultValue: 'Hello World'
        }
      ]
    }
  });

  const watchedFields = watch('fields');

  const generateId = () => Math.random().toString(36).substr(2, 9);

  const generateJSON = (fields: BasicField[]) => {
    const result: any = {};
    fields.forEach(field => {
      if (field.name) {
        result[field.name] = field.type === 'Number' ? 
          (isNaN(Number(field.defaultValue)) ? 0 : Number(field.defaultValue)) : 
          field.defaultValue;
      }
    });
    return result;
  };

  React.useEffect(() => {
    if (watchedFields) {
      const preview = generateJSON(watchedFields);
      setJsonPreview(preview);
    }
  }, [watchedFields]);

  const addField = () => {
    const currentFields = getValues('fields') || [];
    const newField: BasicField = {
      id: generateId(),
      name: '',
      type: 'String',
      defaultValue: ''
    };
    setValue('fields', [...currentFields, newField]);
  };

  const deleteField = (index: number) => {
    const currentFields = getValues('fields') || [];
    const updatedFields = currentFields.filter((_, i) => i !== index);
    setValue('fields', updatedFields);
  };

  return (
    <div style={{
      maxWidth: '1200px',
      margin: '0 auto',
      padding: '24px',
      backgroundColor: '#f5f5f5',
      minHeight: '100vh',
      fontFamily: 'Arial, sans-serif'
    }}>
      <div style={{
        backgroundColor: 'white',
        borderRadius: '8px',
        boxShadow: '0 4px 6px rgba(0, 0, 0, 0.1)'
      }}>
        {/* Header */}
        <div style={{
          borderBottom: '1px solid #e5e5e5',
          padding: '24px'
        }}>
          <h1 style={{
            fontSize: '28px',
            fontWeight: 'bold',
            color: '#333',
            margin: '0 0 8px 0'
          }}>
            JSON Schema Builder
          </h1>
          <p style={{
            color: '#666',
            margin: 0
          }}>
            Build your JSON schema step by step
          </p>
        </div>

        <div style={{ padding: '24px' }}>
          {/* Add Field Button */}
          <div style={{ marginBottom: '24px' }}>
            <button
              onClick={addField}
              style={{
                display: 'inline-flex',
                alignItems: 'center',
                padding: '8px 16px',
                backgroundColor: '#007bff',
                color: 'white',
                border: 'none',
                borderRadius: '6px',
                cursor: 'pointer',
                fontSize: '14px'
              }}
            >
              <span style={{ marginRight: '8px' }}>+</span>
              Add Field
            </button>
          </div>

          <div style={{
            display: 'grid',
            gridTemplateColumns: '1fr 1fr',
            gap: '32px'
          }}>
            {/* Schema Builder */}
            <div>
              <h2 style={{
                fontSize: '20px',
                fontWeight: '600',
                marginBottom: '16px'
              }}>
                Schema Fields
              </h2>
              <div style={{ display: 'flex', flexDirection: 'column', gap: '16px' }}>
                {watchedFields && watchedFields.map((field, index) => (
                  <div key={field.id} style={{
                    border: '1px solid #ddd',
                    borderRadius: '6px',
                    padding: '16px',
                    backgroundColor: '#f9f9f9'
                  }}>
                    <div style={{
                      display: 'grid',
                      gridTemplateColumns: '1fr 100px 1fr 40px',
                      gap: '12px',
                      alignItems: 'center'
                    }}>
                      {/* Field Name */}
                      <div>
                        <Controller
                          name={`fields.${index}.name`}
                          control={control}
                          render={({ field: inputField }) => (
                            <input
                              {...inputField}
                              placeholder="Field name"
                              style={{
                                width: '100%',
                                padding: '8px 12px',
                                border: '1px solid #ccc',
                                borderRadius: '4px',
                                fontSize: '14px'
                              }}
                            />
                          )}
                        />
                      </div>

                      {/* Field Type */}
                      <div>
                        <Controller
                          name={`fields.${index}.type`}
                          control={control}
                          render={({ field: selectField }) => (
                            <select
                              {...selectField}
                              style={{
                                width: '100%',
                                padding: '8px 12px',
                                border: '1px solid #ccc',
                                borderRadius: '4px',
                                fontSize: '14px'
                              }}
                            >
                              <option value="String">String</option>
                              <option value="Number">Number</option>
                            </select>
                          )}
                        />
                      </div>

                      {/* Default Value */}
                      <div>
                        <Controller
                          name={`fields.${index}.defaultValue`}
                          control={control}
                          render={({ field: defaultField }) => (
                            <input
                              {...defaultField}
                              type={field.type === 'Number' ? 'number' : 'text'}
                              placeholder="Default value"
                              style={{
                                width: '100%',
                                padding: '8px 12px',
                                border: '1px solid #ccc',
                                borderRadius: '4px',
                                fontSize: '14px'
                              }}
                            />
                          )}
                        />
                      </div>

                      {/* Delete Button */}
                      <div>
                        <button
                          onClick={() => deleteField(index)}
                          style={{
                            padding: '8px',
                            color: '#dc3545',
                            backgroundColor: 'transparent',
                            border: 'none',
                            borderRadius: '4px',
                            cursor: 'pointer',
                            fontSize: '16px'
                          }}
                          title="Delete field"
                        >
                          ✕
                        </button>
                      </div>
                    </div>
                  </div>
                ))}

                {(!watchedFields || watchedFields.length === 0) && (
                  <div style={{
                    textAlign: 'center',
                    padding: '32px',
                    color: '#666'
                  }}>
                    <p>No fields added yet. Click "Add Field" to get started!</p>
                  </div>
                )}
              </div>
            </div>

            {/* JSON Preview */}
            <div>
              <h2 style={{
                fontSize: '20px',
                fontWeight: '600',
                marginBottom: '16px'
              }}>
                JSON Preview
              </h2>
              <div style={{
                backgroundColor: '#1a1a1a',
                color: '#00ff00',
                padding: '16px',
                borderRadius: '6px',
                fontFamily: 'Monaco, Consolas, monospace',
                fontSize: '12px',
                overflow: 'auto',
                maxHeight: '400px'
              }}>
                <pre>{JSON.stringify(jsonPreview, null, 2)}</pre>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
};

export default JSONSchemaBuilder;
